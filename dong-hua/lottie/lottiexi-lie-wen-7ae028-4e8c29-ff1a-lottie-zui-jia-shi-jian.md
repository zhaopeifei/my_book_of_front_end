# lottie系列文章(二)：lottie最佳实践

## 项目中使用lottie-web的流程
---
### 第一步
新建`src/assets/lottie/`文件夹，并将`bodymovin_light.min.js`[文件](https://cdnjs.com/libraries/bodymovin)和设计师给的动画json文件统一放到`src/assets/lottie/`文件夹中。

另外，请更新`@tencent/im-webpack-common`，因为需要把`src/assets/lottie/`中的json文件打包成单独的文件。如果项目的webpack配置在项目内，需要自行进行配置或者联系payton。

### 第二步
在需要使用lottie的页面中，在其`index.html`中引入`bodymovin.js`。示例如下:
```
{% block preloadScript %}
<script src="../../assets/lottie/bodymovin.min.js" async></script>
{% endblock %}
```

### 第三步
而设计师给的json文件，我们也需要统一放到`src/assets/lottie/`文件夹中。并且通过`require`去获取json文件的url。通过`loadAnimation`方法即可将加载动画。

```
const animateJsonPath = require('assets/lottie/treasurebox.path.json');

if (window.bodymovin) {
  this.animation = window.bodymovin.loadAnimation({
    container: this.box,
    renderer: 'svg',
    loop: true,
    autoplay: true,
    path: animateJsonPath,
  });
}
```

## lottie的js文件及json文件加载方案的考量
---
使用lottie-web的解决方案，需要加载lottie-web的js文件以及动画相应的json文件，这两个文件的大小都比较大，所以我们应该根据需要，确定其加载方式。

### lottie库使用
> lottie-web的npm版本是lottie-web，其对应的cdn外链版则是bodymovin.js

即使是gzip压缩后的lottie-web轻量版的js文件，大小仍然有40kb的大小。如果使用npm包的形式进行加载，那么vendor.js会增加40kb的大小，这样会使页面性能下降。

所以，我们会采用script标签的形式来加载lottie文件，并且使用defer或async属性来进行异步加载。一般情况下，我们只需要svg格式的动画，所以可以使用lottie_light版本（仅支持svg渲染）。

### json文件
通过bodymovin插件导出的动画json文件大小也可能比较大（可能达到几十kb，负责的动画也可能上百kb），所以不建议将json数据内联到页面中，而最好是当做一个json文件来进行下载。

## lottie的使用
---
下面讲一些lottie的常用方法。

### lottie-web的基本用法
```
const animation = window.bodymovin.loadAnimation({
  container: element, // 要包含该动画的dom元素
  renderer: 'svg', // 渲染方式，svg、canvas、html（轻量版仅svg渲染）
  loop: true, // 是否循环播放
  autoplay: true, // 是否自动播放
  path: animateJsonPath, // 动画json文件路径
});
```
### lottie-web常用方法
lottie-web提供了很多的控制动画播放的方法，下面是一些常用的方法。`animation`等于上面代码中的`animation`。

```
animation.play(); // 播放该动画，从目前停止的帧开始播放
animation.stop(); // 停止播放该动画，回到第0帧
animation.pause(); // 暂停该动画，在当前帧停止并保持

animation.goToAndStop(value, isFrame); // 跳到某个时刻/帧并停止。isFrame(默认false)指示value表示帧还是时间(毫秒)
animation.goToAndPlay(value, isFrame); // 跳到某个时刻/帧并进行播放
animation.goToAndStop(30, true); // 跳转到第30帧并停止
animation.goToAndPlay(300); // 跳转到第300毫秒并播放

animation.playSegments(arr, forceFlag); // arr可以包含两个数字或者两个数字组成的数组，forceFlag表示是否立即强制播放该片段
animation.playSegments([10,20], false); // 播放完之前的片段，播放10-20帧
animation.playSegments([[0,5],[10,18]], true); // 直接播放0-5帧和10-18帧

animation.setSpeed(speed); // 设置播放速度，speed为1表示正常速度
animation.setDirection(direction); // 设置播放方向，1表示正向播放，-1表示反向播放
animation.destroy(); // 删除该动画，移除相应的元素标签等。在unmount的时候，需要调用该方法
```

### lottie-web常用的事件
我们在lottie-web中可能也需要监听一些事件，比如加载完动画json文件时的`data_ready`事件。监听方法如下:
```
animation.addEventListener('data_ready', () => {
  console.log('animation data has loaded');
});
```
除了`data_ready`事件，下面还有一些其他常用的事件可以监听：

```
* complete: 播放完成（循环播放下不会触发）
* loopComplete: 当前循环下播放（循环播放/非循环播放）结束时触发
* enterFrame: 每进入一帧就会触发，播放时每一帧都会触发一次，stop方法也会触发
* segmentStart: 播放指定片段时触发，playSegments、resetSegments等方法刚开始播放指定片段时会发出，如果playSegments播放多个片段，多个片段最开始都会触发。
* data_ready: 动画json文件加载完毕触发
* DOMLoaded: 动画相关的dom已经被添加到html后触发
* destroy: 将在动画删除时触发
```

### lottie-web部分高阶用法
1. 在制作AE动画时，将图层命名为`#svgId`格式，前端加载该动画后，相应的图层的id会被设置为`svgId`，可以通过dom方法获取该元素并做相应的操作；
2. 在制作AE动画时，将图层命名为`.svgClass`格式，前端加载该动画后，相应的图层的class会被设置为`svgClass`，可以通过dom方法获取这些元素并做相应的操作；

## 动画json文件制作及导出规范
---
使用该方案制作动画时，有些动画效果lottie-web暂不支持，有些则会影响json文件的大小。

下面这些效果，lottie暂不支持：
1. 描边动效不支持，因为这个属性会导致lottie性能问题，所以后来lottie去掉了对该属性的支持。

遵循下面的方案，会使json文件减小：
1. 尽量减少图层个数。每个图层都会导出成相应的json数据，图层减少能从很大程度上减小json大小。
2. 尽可能所有的图层都是在AE里面画出来的，而不是从其他软件引入的。如果是其他软件引入的，很可能导致描述这个图形的json部分变得很大。

