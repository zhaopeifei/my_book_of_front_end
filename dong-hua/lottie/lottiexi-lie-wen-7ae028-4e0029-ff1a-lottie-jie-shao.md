# lottie系列文章(一)：lottie介绍

## 背景
企鹅辅导是一款处于快速上升期的产品，目前在快速迭代中。作为一款K12青少年教育产品软件，动画对于吸引其用户注意力和提高用户体验有着重要的作用。特别是在目前开放了小学教育内容之后，动画能力的丰富和完善更是迫在眉睫。

目前辅导的动画较少，实现方式一般是：
1. 前端实现svg动画（实现成本较高、维护成本高、容易有买家秀/卖家秀区别、客户端不能复用）；
2. 设计师切gif（文件较大、容易有锯齿）；
3. png序列帧（文件较大、容易有锯齿）；

目前的动画实现方案有着各自的问题，导致目前企鹅辅导的动画并不是很多。同样的问题，也存在于腾讯课堂产品中。

所以，目前迫切的需要寻找一种更加简单、高效、性能高的动画方案。经过调研发现airbnb目前有一套动画——lottie值得研究和应用。

## lottie简介
> Lottie is a mobile library for Web, and iOS that parses Adobe After Effects animations exported as json with Bodymovin and renders them natively on mobile! http://airbnb.io/lottie/

上面是Lottie官方提供的官方效果图。Lottie是一个库，可以解析使用AE制作的动画（需要用bodymovin导出为json格式），支持web、ios、android和react native。在web侧，lottie-web库可以解析导出的动画json文件，并将其以svg或者canvas的方式将动画绘制到我们页面中。

![](/assets/lottie_sum.png)

下面是lottie提供的官方效果图。类似下面这种一段动画的播放，非常适合使用lottie来做。

![](/assets/lottie-demo.gif)

但是，给一个课程卡片本身添加动画（如：从右下角到移动到页面中，并逐渐放大的过渡效果），并不适合使用lottie。


## lottie项目可靠性

lottie是一种动画方案，其包含了lottie-web、lottie-ios等开源项目。它们在github上的情况如下：

项目名称|star|协议|open issues|closed issues|
--|--|--|--|--
lottie-web|11.7k|MIT|258 | 752
lottie-react-native|9k|Apache-2.0|58|199
lottie-android|20.9k|Apache-2.0|38|546
lottie-ios|11.7k|Apache-2.0|111|326

目前Lottie这种动画方案，我们公司已经有部门（增值、QQ音乐、社平、即通等）在研究和使用了。
Lottie是有airbnb开发和维护，其目前star较多，我司也有不少部门都是使用或者尝试。所以，综合认为：lottie是一种可靠性较高的动画方案。


## lottie优点
Lottie方法方案是由设计师出动画，导出为json，给前端播放。所以，使用Lottie方案的好处在于：
* 动画由设计使用专业的动画制作工具Adobe After Effects来实现，使动画实现更加方便，动画效果也更好；
* 前端可以方便的调用动画，并对动画进行控制，减少前端动画工作量；
* 设计制作动画，前端展现动画，专业人做专业事，分工合理；
* 卖家秀即买家秀，还原程度百分之百；
* 使用lottie方案，json文件大小会比gif文件小很多，性能也会更好。

## lottie不足
使用Lottie方案有以下不足之处：
* lottie-web文件本身仍然比较大，lottie.js大小为513k，轻量版压缩后也有144k，经过gzip后，大小为39k。所以，需要注意lottie-web的加载。目前H5项目有离线包，PC项目也会上PWA，会对其进行缓存，保证加载速度。

文件|大小|gzip后
--|--|--
lottie.js|513k| 92k
lottie.min.js|237k| 60k
lottie_light.js (lottie_web轻量版，仅支持svg渲染)|345k| 60k
lottie_lignt.min.js|144k| 39k

* lottie动画其实可以理解为svg动画/canvas动画，不能给已存在的html添加动画效果；
* 动画json文件的导出，目前是将AE里面的参数一一导出成json内容，如果设计师建了很多的图层，可能仍然有json文件比较大（20kb）的问题。需要设计师遵循一定的规范。
* 有很少量的AE动画效果，lottie无法实现，有些是因为性能问题，有些是没有做。比如：描边动画等。

## lottie-web使用方式

#### npm包
`npm install lottie-web`
```
import lottie from 'lottie-web'

lottie.loadAnimation({
  container: element, // the dom element that will contain the animation
  renderer: 'svg',
  loop: true,
  autoplay: true,
  path: 'data.json' // the path to the animation json
});
```

#### 外链js
> lottie-web的npm版本是lottie-web，其对应的cdn外链版则是bodymovin.js

`<script src="https://cdnjs.com/libraries/bodymovin" />`
```
bodymovin.loadAnimation({
  container: element, // the dom element that will contain the animation
  renderer: 'svg',
  loop: true,
  autoplay: true,
  path: 'data.json' // the path to the animation json
});
```

## 功能简介
lottie-web提供了很多操控动画的方法。下面是几种常用的方法，基本可以满足对动画进行控制的需求。
```
play: 播放动画
stop: 停止播放动画
pause: 暂停动画
goToAndStop: 跳到某一帧动画，并停止
setDirection: 设置播放方向
setSpeed: 设置播放速度
```

才外，lottie-web也提供了一系列的事件，例如：
```
onComplete: 动画播放完成触发
onLoopComplete: 当前循环播放完成触发
onEnterFrame: 播放一帧动画的时候触发
onSegmentStart: 开始播放一个动画片段的时候触发
```

总体而言，lottie-web目前提供了丰富的方法和事件，基本可以满足对动画进行控制的需求。

## lottie原理简介

在Adobe After Effects中，动画是由一个一个的图层组成的，在相应的图层上面添加一些变换(例如：缩放、移动等)，这变成为了动画。下面是一个AE图层截图：

![](/assets/ae.png)

例如，“右眼 眨”便是一个图层，里面包含内容(图层形状等)和变换。将该动画通过bodymovin插件导出的json，会如以下格式：

```
{
    "fr": 30, // 帧率
    "ip": 0, // 起始关键帧
    "op": 20, // 结束关键帧
    "w": 150, // 视图宽
    "h": 130, // 视图高
    "assets": [], // 资源集合 
    "layers": [{ // 图层
        "ty": 0, // 图层类型。
        "refId": "comp_0", // 引用的资源，图片/预合成层
        "ks": {}, // 变换。对应AE中的变换设置，下面有详细介绍
        layer: [], // 该图层包含的子图层
        shapes: [], // 形状图层
        "w": 1334,
        "h": 750,
        "bm": 0
    }], // 图层集合
    "masker": [] // 蒙层集合
}
```

动画的json文件中，主要内容是layers，对应AE中的一个个图层。每个图层里面包含内容（shapes）、变换（ks）、子图层（layers），与AE中图层是一一对应的。

在lottie-web中，会根据上面的json，进行相应的渲染处理。例如: 
1. shapes中会有各种形状，lottie-web会根据其参数，渲染出相应的svg标签；
2. ks中会有变换参数，lottie-web会将其转换成相应的trasform属性，添加到对应svg标签上；
3. ks的变换参数可能随时间变换，lottie-web会根据参数，调用window.requestAnimationFrame方法，对transform属性进行动态更新，实现动画。