# lottie系列文章(四)：源码分析——svg渲染

## 动画管理相关源码
lottie为全局变量，主要有一个loadAnimation的方法，来加载和解析json，播放动画。

```
var animationManager = function

(function(root, factory) {
  root.lottie = factory(root)
}(window, function(window) {
  function loadAnimation() {
    return animationManager.loadAnimation(params);
  }
}));
```

由上面的代码可以看出来，每次调用lottie.laodAnimation，其实是去调用animationManager的loadAnimation方法。
```
var animationManager = (function(){
    function loadAnimation(params){
        ar animItem = new AnimationItem(); // 新建对象
        setupAnimation(animItem, null); // 主要是添加一些时间监听函数
        animItem.setParams(params); // 根据输入的参数和json数据，渲染成相应的动画
        return animItem;
    }
});
```
全局只有一个animationManager对象，其用于管理我们加载到页面中动画实例。而我们加载到页面中的每个动画，其实是AnimtionItem类的一个实例。一个AnimationItem实例，包含了很多的属性，比如：playSpeed、playDirection、loop等等（不仅仅是下面代码中的属性，还有大量的属性在下面的代码中没有罗列出来）。

```
var AnimationItem = function () {
    this.playSpeed = 1;
    this.playDirection = 1;
    this.isPaused = true;
    this.autoplay = false;
    this.loop = true;
}

AnimationItem.prototype.setParams = function(params) {
    var animType = params.animType ? params.animType : params.renderer ? params.renderer : 'svg';
    switch(animType){
        case 'canvas':
            this.renderer = new CanvasRenderer(this, params.rendererSettings);
            break;
        case 'svg':
            this.renderer = new SVGRenderer(this, params.rendererSettings);
            break;
        default:
            this.renderer = new HybridRenderer(this, params.rendererSettings);
            break;
    }
}
```

而每个AnimtionItem实例，会根据我们的传参，去渲染成对应的svg动画或者是canvas动画等。而上面列中的代码中的SVGRenderer、CanvasRenderer是lottie的主要内容。它们负责将json参数解析为对应的SVG元素、canvas元素，进而实现动画的播放。

## SVGRenderer
上面说到，SVGRenderer、CanvasRenderer和HybridRenderer是lottie的重要组成部分，本部分内容以SVGRenderer为例，尝试为大家展示出如何根据json参数来渲染svg元素的步骤和方法。

### 最外层json
```
{
    "v": "5.1.13", // bodymovin 版本
    "fr": 30, // 帧率
    "ip": 0, // 起始关键帧
    "op": 20, // 结束关键帧
    "w": 150, // 视图宽
    "h": 130, // 视图高
    "nm": "鹅头收起动画", // 名称
    "ddd": 0, // 3d
    "assets": [], // 资源集合 
    "layers": [], // 图层集合
    "masker": [] // 蒙层集合
}
```

上图为一个动画json文件，上面给出了各个参数的含义。其中ip表示其实关键帧，一般为0，op表示动画的结束关键帧，fr表示帧率，所以动画时间等于：`(op-ip)/fr` 。w和h分别表示视图的宽和高。

由于assets、layers、masker里面的数据可能很大，所以上面用空数组代替。其中layers是一个图层集合，它里面数据一般很大，里面包含了当前动画的所有图层数据，assets是一个资源集合，它里面包含了当前动画使用的资源图层数据。masks则表示蒙层集合，里面包含了所有的蒙层数据。

在lottie-web中，处理以上这些数据的代码如下所示（删除了一些相关性不强的代码，完整的代码请看lottie-web源码）：
```
SVGRenderer.prototype.configAnimation = function(animData){
    // 设置svg大小
    if(this.renderConfig.viewBoxSize) {
        this.svgElement.setAttribute('viewBox',this.renderConfig.viewBoxSize);
    } else {
        this.svgElement.setAttribute('viewBox','0 0 '+animData.w+' '+animData.h);
    }
    
    this.animationItem.wrapper.appendChild(this.svgElement);

    this.setupGlobalData(animData, defs); // 根据json，设置相关参数

    this.layers = animData.layers; // 图层数据
    this.elements = createSizedArray(animData.layers.length); // 创建元素
};

BaseRenderer.prototype.setupGlobalData = function(animData, fontsContainer) {
    this.globalData.fontManager = new FontManager(); // 字体管理器
    this.globalData.fontManager.addChars(animData.chars);
    this.globalData.fontManager.addFonts(animData.fonts, fontsContainer);
    this.globalData.getAssetData = this.animationItem.getAssetData.bind(this.animationItem); // 设置资源数据
    this.globalData.getAssetsPath = this.animationItem.getAssetsPath.bind(this.animationItem);
    this.globalData.elementLoaded = this.animationItem.elementLoaded.bind(this.animationItem);
    this.globalData.addPendingElement = this.animationItem.addPendingElement.bind(this.animationItem);
    this.globalData.frameId = 0;
    this.globalData.frameRate = animData.fr; // 帧率
    this.globalData.nm = animData.nm; // 名称
    this.globalData.compSize = {
        w: animData.w,
        h: animData.h
    }
}
```

### 图层元素 layer
动画是由一个一个的图层组合起来，并在图层上进行偏移、缩放等操作来实现动画的。图层的解析是lottie的主要功能模块。
一个layer图层的数据格式一般如下：

```
{
    "ddd": 0, // 是否为3d
    "ind": 1, // layer的ID，唯一
    "ty": 0, // 图层类型。
    "nm": "鹅头收起", // 图层名称
    "refId": "comp_0", // 引用的资源，图片/预合成层
    "sr": 1,
    "ks": {}, // 变换。对应AE中的变换设置，下面有详细介绍
    layer: [], // 该图层包含的子图层
    shaps: [], // 形状图层
    "ao": 0,
    "w": 1334,
    "h": 750,
    "ip": 0, // 该图层开始关键帧
    "op": 60, // 该图层结束关键帧
    "st": 0, // 该图层
    "bm": 0
}
```
上面是一个layer图层的object的格式。

其中说明一下nm属性，该属性是在AE中对该图层的命名，通过在SVG中修改该命名，可以设置对应的svg的class和id。如果命名为'#svgId'，生成的对应的svg元素的id则为'svgId'；如果命名为'.svg-class'，则生成的对应的svg元素的class为'svg-class'。

ty表示类型，例如：
* 2: image，图片
* 0: comp，合成图层
* 1: solid; 
* 3: null; 
* 4: shape，形状图层
* 5: text，文字

lottie-web中对layers图层数据相应的处理有：
```
BaseRenderer.prototype.buildAllItems = function(){ // 开始创建所有的元素
    var i, len = this.layers.length;
    for(i=0;i<len;i+=1){
        this.buildItem(i);
    }
};

SVGRenderer.prototype.buildItem  = function(pos){
    var elements = this.elements;
    var element = this.createItem(this.layers[pos]); // 创建元素

    elements[pos] = element; // 将元素添加到svg中
    this.appendElementInPos(element,pos);
};
```
```
BaseRenderer.prototype.createItem = function(layer){
    switch(layer.ty){ // 根据图层类型，创建相应的svg元素类的实例
        case 2:
            return this.createImage(layer);
        case 0:
            return this.createComp(layer);
        case 1:
            return this.createSolid(layer);
        case 3:
            return this.createNull(layer);
        case 4:
            return this.createShape(layer);
        case 5:
            return this.createText(layer);
        case 13:
            return this.createCamera(layer);
    }
    return this.createNull(layer);
};

SVGRenderer.prototype.createNull = function (data) {
    return new NullElement(data,this.globalData,this);
};
SVGRenderer.prototype.createShape = function (data) {
    return new SVGShapeElement(data,this.globalData,this);
};
SVGRenderer.prototype.createText = function (data) {
    return new SVGTextElement(data,this.globalData,this);
};
SVGRenderer.prototype.createImage = function (data) {
    return new IImageElement(data,this.globalData,this);
};
SVGRenderer.prototype.createComp = function (data) {
    return new SVGCompElement(data,this.globalData,this);
};
SVGRenderer.prototype.createSolid = function (data) {
    return new ISolidElement(data,this.globalData,this);
};
```

下面以`SVGCompElement`类为例，展示如何创建相应的实例的。

```
function SVGCompElement(data,globalData,comp){ // 合成图层
    this.layers = data.layers; // 包含的图层

    this.elements = this.layers ? createSizedArray(this.layers.length) : []; // 后面会根据layers，创建子元素
    this.initElement(data,globalData,comp);
    this.tm = data.tm ? PropertyFactory.getProp(this,data.tm,0,globalData.frameRate,this) : {_placeholder:true};
}
```

```
ICompElement.prototype.initElement = function(data,globalData,comp) {
    this.initFrame();
    this.initBaseData(data, globalData, comp); // 设置该图层参数
    this.initTransform(data, globalData, comp); // 获取transform相关的数据
    this.initRenderable();
    this.initHierarchy();
    this.initRendererElement();
    this.createContainerElements(); // 创建一个会包含子元素的g元素，并根据前面初始化的参与，设置g元素的属性（transform、filter、蒙层、id等）
    this.addMasks();
    if(this.data.xt || !globalData.progressiveLoad){
        this.buildAllItems(); // 将子图层转换为相应的元素
    }
    this.hide();
};
```

#### ks变换
ks对应AE中图层的变换属性，可以通过设置锚点、位置、旋转、缩放、透明度等来控制图层，并设置这些属性的变换曲线，来实现动画。下面是一个ks属性值：

```
"ks": { // 变换。对应AE中的变换设置
    "o": { // 透明度
        "a": 0,
        "k": 100,
        "ix": 11
    },
    "r": { // 旋转
        "a": 0,
        "k": 0,
        "ix": 10
    },
    "p": { // 位置
        "a": 0,
        "k": [-167, 358.125, 0],
        "ix": 2
    },
    "a": { // 锚点
        "a": 0,
        "k": [667, 375, 0],
        "ix": 1
    },
    "s": { // 缩放
        "a": 0,
        "k": [100, 100, 100],
        "ix": 6
    }
}
```

lottie-web会把ks处理成transform的属性，用于对元素进行变换操作。transform包含了translate（平移）、scale（缩放）、rotate（旋转）、skew（倾斜）等几种。lottie-web中处理ks（变换）的相关代码为：

```
function getTransformProperty(elem,data,container){ // data为ks数据
    return new TransformProperty(elem,data,container);
}

function TransformProperty(elem,data,container){
    this.elem = elem;
    this.frameId = -1;
    this.propType = 'transform';
    this.data = data;
    this.v = new Matrix();
    //Precalculated matrix with non animated properties
    this.pre = new Matrix();
    this.appliedTransformations = 0;
    this.initDynamicPropertyContainer(container || elem);
    if(data.p.s){ // 获取translate相关的参数
        this.px = PropertyFactory.getProp(elem,data.p.x,0,0,this); 
        this.py = PropertyFactory.getProp(elem,data.p.y,0,0,this);
        if(data.p.z){
            this.pz = PropertyFactory.getProp(elem,data.p.z,0,0,this);
        }
    }else{
        this.p = PropertyFactory.getProp(elem,data.p,1,0,this);
    }
    if(data.r) { // 获取rotate相关的参数
        this.r = PropertyFactory.getProp(elem, data.r, 0, degToRads, this);
    } else if(data.rx) {
        this.rx = PropertyFactory.getProp(elem, data.rx, 0, degToRads, this);
        this.ry = PropertyFactory.getProp(elem, data.ry, 0, degToRads, this);
        this.rz = PropertyFactory.getProp(elem, data.rz, 0, degToRads, this);
        if(data.or.k[0].ti) {
            var i, len = data.or.k.length;
            for(i=0;i<len;i+=1) {
                data.or.k[i].to = data.or.k[i].ti = null;
            }
        }
        this.or = PropertyFactory.getProp(elem, data.or, 1, degToRads, this);
        //sh Indicates it needs to be capped between -180 and 180
        this.or.sh = true;
    }
    if(data.sk){ // 获取skew相关的参数
        this.sk = PropertyFactory.getProp(elem, data.sk, 0, degToRads, this);
        this.sa = PropertyFactory.getProp(elem, data.sa, 0, degToRads, this);
    }
    if(data.a) { // 获取translate相关的参数
        this.a = PropertyFactory.getProp(elem,data.a,1,0,this);
    }
    if(data.s) { // 获取scale相关的参数
        this.s = PropertyFactory.getProp(elem,data.s,1,0.01,this);
    }

    if(data.o){ // 透明度
        this.o = PropertyFactory.getProp(elem,data.o,0,0.01,elem);
    } else {
        this.o = {_mdf:false,v:1};
    }
    this._isDirty = true;
    if(!this.dynamicProperties.length){
        this.getValue(true);
    }
}
```

```
function applyToMatrix(mat) {
    var _mdf = this._mdf;
    this.iterateDynamicProperties();
    this._mdf = this._mdf || _mdf;
    if (this.a) {
        mat.translate(-this.a.v[0], -this.a.v[1], this.a.v[2]);
    }
    if (this.s) {
        mat.scale(this.s.v[0], this.s.v[1], this.s.v[2]);
    }
    if (this.sk) {
        mat.skewFromAxis(-this.sk.v, this.sa.v);
    }
    if (this.r) {
        mat.rotate(-this.r.v);
    } else {
        mat.rotateZ(-this.rz.v).rotateY(this.ry.v).rotateX(this.rx.v).rotateZ(-this.or.v[2]).rotateY(this.or.v[1]).rotateX(this.or.v[0]);
    }
    if (this.data.p.s) {
        if (this.data.p.z) {
            mat.translate(this.px.v, this.py.v, -this.pz.v);
        } else {
            mat.translate(this.px.v, this.py.v, 0);
        }
    } else {
        mat.translate(this.p.v[0], this.p.v[1], -this.p.v[2]);
    }
}
```

#### shape
shape参数的值，对应AE中图层的内容中的形状设置的内容，其主要用于绘制图形。下面一个shape的json为例：

```
"shapes": [{
  "ty": "gr", // 类型。混合图层
  "it": [{ // 各图层json
      "ind": 0,
      "ty": "sh", // 类型，sh表示图形路径
      "ix": 1,
      "ks": {
          "a": 0,
          "k": {
              "i": [ // 内切线点集合
                  [0, 0],
                  [0, 0]
              ],
              "o": [ // 外切线点集合
                  [0, 0],
                  [0, 0]
              ],
              "v": [ // 顶点坐标集合
                  [182, -321.75],
                  [206.25, -321.75]
              ], 
              "c": false // 贝塞尔路径闭合
          },
          "ix": 2
      },
      "nm": "路径 1",
      "mn": "ADBE Vector Shape - Group",
      "hd": false
  },{
    "ty": "st", // 类型。图形描边
    "c": { // 线的颜色
        "a": 0,
        "k": [0, 0, 0, 1],
        "ix": 3
    },
    "o": { // 线的不透明度
        "a": 0,
        "k": 100,
        "ix": 4
    },
    "w": { // 线的宽度
        "a": 0,
        "k": 3,
        "ix": 5
    },
    "lc": 2, // 线段的头尾样式
    "lj": 1, // 线段的连接样式
    "ml": 4, // 尖角限制
    "nm": "描边 1",
    "mn": "ADBE Vector Graphic - Stroke",
    "hd": false
  }]
}]
```

上面是一个shape形状的json示例，可以看出不同的shape类型，参数也不同。shape对应的是AE中的图层的内容的设置。shape中的ty字段表示shape的类型，ty有以下几种：

* gr: 图形合并
* st: 图形描边
* fl: 图形填充
* tr: 图形变换
* sh: 图形路径
* el: 椭圆路径
* rc: 矩形路径
* tm: 剪裁路径

```
var SVGElementsRenderer = (function() {
	function createRenderFunction(data) {
	    var ty = data.ty;
	    switch(data.ty) { // 根据类型，进行相应的渲染
	        case 'fl':
	        return renderFill;
	        case 'gf':
	        return renderGradient;
	        case 'gs':
	        return renderGradientStroke;
	        case 'st':
	        return renderStroke;
	        case 'sh':
	        case 'el':
	        case 'rc':
	        case 'sr':
	        return renderPath;
	        case 'tr':
	        return renderContentTransform;
	    }
	}

    // 渲染path元素
    function renderPath(styleData, itemData, isFirstFrame) {
	    var j, jLen,pathStringTransformed,redraw,pathNodes,l, lLen = itemData.styles.length;
	    var lvl = itemData.lvl;
	    var paths, mat, props, iterations, k;
	    for(l=0;l<lLen;l+=1){
	        redraw = itemData.sh._mdf || isFirstFrame;
	        if(itemData.styles[l].lvl < lvl){ // 设置transform等属性
	            mat = _matrixHelper.reset();
	            iterations = lvl - itemData.styles[l].lvl;
	            k = itemData.transformers.length-1;
	            while(!redraw && iterations > 0) {
	                redraw = itemData.transformers[k].mProps._mdf || redraw;
	                iterations --;
	                k --;
	            }
	            if(redraw) {
	                iterations = lvl - itemData.styles[l].lvl;
	                k = itemData.transformers.length-1;
	                while(iterations > 0) {
	                    props = itemData.transformers[k].mProps.v.props;
	                    mat.transform(props[0],props[1],props[2],props[3],props[4],props[5],props[6],props[7],props[8],props[9],props[10],props[11],props[12],props[13],props[14],props[15]);
	                    iterations --;
	                    k --;
	                }
	            }
	        } else {
	            mat = _identityMatrix;
	        }
	        paths = itemData.sh.paths;
	        jLen = paths._length;
	        if(redraw){ // 设置path的d的值，即路径绘制形状
	            pathStringTransformed = '';
	            for(j=0;j<jLen;j+=1){
	                pathNodes = paths.shapes[j];
	                if(pathNodes && pathNodes._length){
	                    pathStringTransformed += buildShapeString(pathNodes, pathNodes._length, pathNodes.c, mat);
	                }
	            }
	            itemData.caches[l] = pathStringTransformed;
	        } else {
	            pathStringTransformed = itemData.caches[l];
	        }
	        itemData.styles[l].d += pathStringTransformed;
	        itemData.styles[l]._mdf = redraw || itemData.styles[l]._mdf;
	    }
	}

    // 绘制stroke（描边的线）
    function renderStroke(styleData, itemData, isFirstFrame) {
	    var styleElem = itemData.style;
	    var d = itemData.d;
	    if (d && (d._mdf || isFirstFrame) && d.dashStr) { // 虚线
	        styleElem.pElem.setAttribute('stroke-dasharray', d.dashStr);
	        styleElem.pElem.setAttribute('stroke-dashoffset', d.dashoffset[0]);
	    }
	    if(itemData.c && (itemData.c._mdf || isFirstFrame)){ // 线的颜色
	        styleElem.pElem.setAttribute('stroke','rgb(' + bm_floor(itemData.c.v[0]) + ',' + bm_floor(itemData.c.v[1]) + ',' + bm_floor(itemData.c.v[2]) + ')');
	    }
	    if(itemData.o._mdf || isFirstFrame){ // 透明度
	        styleElem.pElem.setAttribute('stroke-opacity', itemData.o.v);
	    }
	    if(itemData.w._mdf || isFirstFrame){ // 线宽
	        styleElem.pElem.setAttribute('stroke-width', itemData.w.v);
	        if(styleElem.msElem){
	            styleElem.msElem.setAttribute('stroke-width', itemData.w.v);
	        }
	    }
	};
}
```


  


