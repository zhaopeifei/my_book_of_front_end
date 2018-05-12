# Lottie: 让动画如此简单

> Render After Effects animations natively on Web, Android and iOS, and React Native. [http://airbnb.io/lottie/](http://airbnb.io/lottie/)

Lotties是由Airbnb推出的一个，能够在Web、Android、iOS和RN等平台渲染Adobe After Effects软件制作的动画的库。使用这种解决方案，设计师出一份动画，就可以在各个端实现动画效果了。目前该库在Github上累计已经有了几万的star，腾讯内容有些团队已经在使用了，稳定性相对较好。官方效果图如下：

![](https://blog-10039692.file.myqcloud.com/1504855429015_184_1504855429322.gif)

## Lottie简介

---

Lottie解决方案其中，动画是由设计师实现的，各个端仅仅负责使用Lottie相应的库提供的方法进行渲染。具体步骤如下：

1. 设计师通过Adobe After Effects软件制作动画；
2. 设计师通过AE的bodymovin插件将制作好的动画导出为JSON文件，bodymovin插件的按照参考[相关GitHub](https://github.com/bigxixi/bodymovin)；
3. 开发拿到JSON文件后，通过lottie库提供的方法解析JSON文件，然后渲染出来。

由上面的步骤可以看出，这种解决方案，其实是由设计师来设计动画，开发仅仅负责渲染和动画控制，更符合“让专业的人做专业的事”的理念。并且，使用这种方案后，就不需要各个端自己来实现相应的动画，设计师的一份动画就可以在各个端愉快的跑起来了。

不同的端使用的lottie库自然是不同的，前端开发使用[lottie-web库，](https://github.com/airbnb/lottie-web)Android使用[lottie-android](https://github.com/airbnb/lottie-android)，iOS使用[lottie-ios](https://github.com/airbnb/lottie-ios)，React Native使用[lottie-react-native](https://github.com/airbnb/lottie-react-native)。

## Lottie前端引用

---

### 引用Lottie库的方式

前端使用Lottie有以下三种方式：

```
# with npm
npm install lottie-web

# with bower
bower install bodymovin

# CDN: https://cdnjs.com/libraries/bodymovin
<script src="https://cdnjs.cloudflare.com/ajax/libs/bodymovin/4.13.0/bodymovin.js" />
```

使用npm需要引入的lottie-web库，bower和cdn引用方式是使用bodymovin文件。两者之间有部分不同的代码，但是在功能上并没有区别，可以将二者认为是同一个文件。

### 文件大小

| 文件名称 | 大小 |
| :--- | :--- |
| bodymovin.js | 92.1KB\(gzip之后\) |
| bodymovin.min.js | 60.6KB\(gzip且压缩后\) |
| bodymovin\_light.js | 60.6KB（gzip之后） |
| bodymovin\_light.min.js | 39.1KB（gzip且压缩后） |
| lottie.js | 513KB\(未经过gzip\) |
| lottie.min.js | 237KB（压缩后） |
| lottie\_light.js | 345KB（未经过gzip） |
| lottie\_light.min.js | 144KB（压缩后） |

各文件大小如上表所示，bodymovin.js的源文件大小基本和lottie.js一直，但是经过gzip之后可以缩小很多，同理，lottie.js经过gzip之后，也会缩小很多。再次强调一下，本质上bodymovin.js和lottie.js对应的文件并没有区别。

`bodymovin_light.js`和`lottie_light.js`是lottie官方提供的轻量版的库。相比lottie，轻量版的littie仅支持svg渲染，不支持canvas和html渲染，也不支持表达式。

由于lottie库文件比较大，并且有些动画并非一进入页面就需要渲染。所以，个人建议，可以将bodymovin.min.js放到cdn上，然后使用script标签引入html。

## Lottie前端使用方法

---

### 基本方法

```
// 如果使用的lottie-web，则使用lottie.loadAnimation
var animation = bodymovin.loadAnimation({
  container: document.getElementById('bm'), // 要渲染的元素位置
  renderer: 'svg', // 渲染方式:svg/canvas/html，默认为svg
  loop: true, // 循环播放，true/false/number
  autoplay: true, // 自动播放, true/false
  path: 'data.json', // json文件路径
  animationData: animationData, // 也可以设置一个对象，而不设置path
  name: 'test', // 动画名称
})
```

### 更多方法

将动画json文件引入后，我们就获得了该动画对应的对象。该对象上会有很多方法来控制动画的播放。下面我们将介绍几个比较常用的方法。

```
var anim = bodymovin.loadAnimation({...});
anim.play(); // 播放动画，从当前帧播放到最后一帧
anim.stop(); // 停止播放
anim.pause(); // 暂停
anim.setSpeed(speed); // speed: 播放速度，数字，1代表正常速度
anim.goToAndStop(value, isFrame); // value:数字，isframe：第一个参数是否是帧
anim.goToAndPlay(value, isFrame); //
anim.setDirection(direction); // direction:1表示正向，-1表示反向
anim.playSegments(segments, forceFlag); // 播放帧片段
anim.destroy(); 
anim.getDuration(inFrames); // 返回动画帧总数或者时长
```

## 参考文章

---

* Github：[https://github.com/airbnb/lottie-web](https://github.com/airbnb/lottie-web)

* [大杀器Bodymovin和Lottie：把AE动画转换成HTML5/Android/iOS原生动画](https://zhuanlan.zhihu.com/p/26304609)



