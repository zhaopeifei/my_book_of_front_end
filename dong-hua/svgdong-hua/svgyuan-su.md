# SVG形状元素 {#svg%E5%BD%A2%E7%8A%B6%E5%85%83%E7%B4%A0}

## 简单形状的SVG元素 {#%E7%AE%80%E5%8D%95%E5%BD%A2%E7%8A%B6%E7%9A%84svg%E5%85%83%E7%B4%A0}

---

### Line元素 {#line%E5%85%83%E7%B4%A0}

显然，line元素在SVG中用于绘制直线。`<line>`有四个属性表示如何绘制属性：

* x1,y1表示直线起点坐标；
* x2,y2表示直线终点坐标。

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <line x1="10"  y1="10" x2="1000"  y2="10" style="stroke:red;"/>
  <line x1="10" y1="10" x2="1000" y2="100" style="stroke:black;"/>
</svg>
```

![](/assets/line.png)

### Rect元素 {#rect%E5%85%83%E7%B4%A0}

SVG的`<rect>`元素表示矩形。`<rect>`有六个属性表示如何绘制矩形/圆角矩形：

* x,y表示矩形左上角的坐标；
* width,height表示矩形的宽和高；
* rx,ry表示矩形圆角半径，如果省略了rx，则ry默认等于rx。

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <rect x="10" y="10" height="50" width="50" style="fill: #00C341"/>
  <rect x="70" y="10" height="50" width="50" rx="10" style="fill: #00C341" />
  <rect x="130" y="10" height="50" width="50" rx="5" ry="15" style="fill: #00C341"/>
</svg>
```

![](/assets/rect.png)

### Circle元素 {#circle%E5%85%83%E7%B4%A0}

SVG的`<circle>`元素用于绘制圆形。`<circle>`元素有三个属性表示如何绘制圆形：

* cx,cy表示圆形的圆点坐标；
* r表示圆半径。

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <circle cx="100" cy="100" r="50" fill="#00C341" />
  <circle cx="150" cy="100" r="50" stroke="black" fill="none" />
  <circle cx="200" cy="100" r="50" fill="#00C341" />
</svg>
```

![](/assets/circle.png)

### Ellipse元素 {#ellipse%E5%85%83%E7%B4%A0}

SVG的`<ellipse>`元素用于绘制椭圆。`<ellipse>`元素有四个属性表示如何绘制椭圆：

* cx,cy表示椭圆的圆点坐标；
* rx,ry分别表示椭圆横坐标、纵坐标的半径，rx，ry默认值均为0，所以一定要设置。

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <ellipse cx="100" cy="100" rx="50" ry="25" fill="#00C341" />
  <ellipse cx="150" cy="100" rx="50" ry="25" stroke="black" fill="none" />
  <ellipse cx="200" cy="100" rx="50" ry="25" fill="#00C341" />
</svg>
```

![](/assets/ellipse.png)

### Polyline元素 {#polyline%E5%85%83%E7%B4%A0}

SVG的`<polyline>`元素用于绘制折线。`<polyline>`元素只有一个`points`属性表示如何绘制折线。`points`的值为成对出现的坐标值，表示折线的各个点。

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <polyline points="10,10  100,10  50,100" stroke="#00C341" fill="none"/>
  <polyline points="110,10  200,10  150,100 110,10" stroke="#00C341" fill="none"/>
</svg>
```

![](/assets/polyline.png)

### Polygon元素 {#polygon%E5%85%83%E7%B4%A0}

SVG的`<polygon>`元素用于绘制多边形。类似于`<polyline>`元素，`<polygon>`元素也只有一个`points`属性表示如何绘制多边形。`points`的值同样是成对出现的坐标值，表示多边形的各个点。`<polygon>`元素与`<polyline>`元素的区别在于：`<polygon>`会自动添加一条最后一个点到第一个点的直线，使图形封口。

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <polygon points="10,10  100,10  50,100" stroke="#00C341" fill="none"/>
  <polygon points="110,10  200,10  250,100 150,100" stroke="#00C341" fill="none"/>
</svg>
```

![](/assets/polygon.png)

## 高级形状的Path元素 {#%E9%AB%98%E7%BA%A7%E5%BD%A2%E7%8A%B6%E7%9A%84path%E5%85%83%E7%B4%A0}

---

SVG还提供了`<path>`元素来绘制组合线条、弧线、曲线等高级形状。所有的基本形状元素都可以由`<path>`元素画出来，此外，所有的形状都可以由`<path>`元素绘制出来。使用sketch等软件绘制的矢量图，其中的形状通常也都是转换成`<path>`元素。`<path>`元素是SVG的重要元素，也是最难掌握的元素。

`<path>`元素中，使用属性d来表示如何绘制形状。属性d的值由命令和坐标组成，命令表示如何绘制什么形状的图形，坐标表示在什么位置绘制图形。具体的命令下面将详细介绍。

命令字都是单个单词，如M、m、L、l等，每个命令词都有大小写。大写的明显表示绝对坐标，小写表示相对当前虚拟画笔的相对位置。下面将不再进行说明。

### 移动画笔&绘制直线 {#%E7%A7%BB%E5%8A%A8%E7%94%BB%E7%AC%94%E7%BB%98%E5%88%B6%E7%9B%B4%E7%BA%BF}

移动画笔的命令是M（或者m），即move的简写，命令字后面跟一个坐标值，坐标将画笔移动到这个位置。

绘制直线的命令是L（或者l），即line的简写，命令字后面跟一个坐标值，表示从当前画笔的绘制一条直线到这个位置。绘制完成后，画笔的位置变为直线的终点的位置，完成其他命令的绘制后，画笔的位置也同样会变成绘制完成后的终点绘制。

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <path d="M10,10 L1000,10 M10,10 L1000,100" style="stroke:#660000;"/>
</svg>
```

![](/assets/line1.png)

### 绘制弧线 {#%E7%BB%98%E5%88%B6%E5%BC%A7%E7%BA%BF}

绘制弧线的命令是A（或者a），即arc简写，命令字后面跟7个参数，按照顺序，其含义分别代表：

* rx，ry：前两个参数分别表示x方向和y方向的半径；
* x-axis-rotation：第三个参数表示相对正常x轴旋转一定角度的弧的x轴；
* large-arc-flag，sweep-flag：第四个参数，1表示是大圆弧，0表示小圆弧。第五个参数，1表示顺时针绘制，0表示逆时针绘制；
* x，y：表示弧线的终点坐标

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <path d="M40,20  A30,30 0 0,0 60,70" stroke="red" fill="none"/>
  <path d="M40,20  A30,30 0 1,0 60,70" stroke="green" fill="none"/>
  <path d="M40,20  A30,30 0 1,1 60,70" stroke="blue" fill="none"/>
  <path d="M40,20  A30,30 0 0,1 60,70" stroke="black" fill="none"/>
</svg>
```

![](/assets/arc.png)

### 贝塞尔曲线 {#%E8%B4%9D%E5%A1%9E%E5%B0%94%E6%9B%B2%E7%BA%BF}

`<path>`元素也可以绘制[贝塞尔曲线](https://zh.wikipedia.org/wiki/%E8%B2%9D%E8%8C%B2%E6%9B%B2%E7%B7%9A)，贝塞尔曲线是被广泛应用的一种曲线。`<path>`元素可以绘制二次别赛尔曲线和三次贝塞尔曲线。

绘制二次贝塞尔曲线的命令是Q（或者q），其后面跟四个参数：

* 前两个参数表示控制点的坐标；
* 后两个参数表示绘制终点。

绘制三次贝塞尔曲线的命令是C（或者c），其后面跟六个参数：

* 前两个参数表示第一个控制点的坐标；
* 中间两个参数表示第二个控制点的坐标；
* 最后两个参数表示绘制终点。

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <path d="M50,50 Q50,100 100,100" stroke="#00C341" fill="none"/>
  <path d="M150,75 c50,-50 100,50 150,0" stroke="#00C341" fill="none"/>
  <circle cx="50" cy="100" r="1" fill="black" />
  <circle cx="200" cy="25" r="1" fill="black" /> 
  <circle cx="250" cy="125" r="1" fill="black" /> 
</svg>
```

![](/assets/q.png)

### 闭合路径 {#%E9%97%AD%E5%90%88%E8%B7%AF%E5%BE%84}

`<path>`元素提供了Z（或者z）命令来闭合曲线，即从当前画笔重点绘制一条到路径起点的直线。此外，`<path>`元素的d属性中，可以使用多个命令，即组合上面的命令。

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <path d="M10,10  L100,10  L50,100 z" stroke="#00C341" fill="none"/>
  <path d="M110,10  q50,100 100,0 z" stroke="#00C341" fill="none"/>
</svg>
```

![](/assets/z.png)

### 简写 {#%E7%AE%80%E5%86%99}

`<path>`元素的d属性有很多简写方式。

* 如果需要多次使用相同的命令可以省略命令字母，仅需要提供一组额外的参数；
* 如果参数是负值，可以省略参数前的空格和逗号；
* 命令字前后的空格可以省略；

以下三个path元素绘制出来的图形是等价的：

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <path d="M10,10l100,0 0,50-100,0 0-50" stroke="#00C341" fill="none"/>
  <path d="M10,10 l100,0 0,50 -100,0 0,-50" stroke="#00C341" fill="none"/>
  <path d="M10,10 l100,0 l0,50 l-100,0 l0,-50" stroke="#00C341" fill="none"/>
</svg>
```

### 命令字及参数含义 {#%E5%91%BD%E4%BB%A4%E5%AD%97%E5%8F%8A%E5%8F%82%E6%95%B0%E5%90%AB%E4%B9%89}

命令字均有大小写之分，大写的命令字后的参数表示的都是绝对坐标，而小写的命令字后的参数表示的都是相对于画笔当前位置的相对坐标值。由于大小写命令字后的参数表示含义一致，所以就下表中就不写小写命令字了。

| 命令 | 参数 | 名称 | 描述 |
| :--- | :--- | :--- | :--- |
| M | x,y | 移至 | 将虚拟画笔移动到指定点x,y处而不绘制图。 |
| m | x,y | 移至 | 将虚拟画笔移动至相对于其当前坐标的x,y处而不进行绘图操作。 |
| L | x,y | 线路 | 从虚拟画笔当前位置绘制一条到x,y点的直线。 |
| H | x | 水平线 | 绘制一条到指定点（x坐标由参数指定，y坐标为虚拟画笔当前的纵坐标）的水平线。 |
| V | y | 垂直线 | 绘制一条到指定点（x坐标为虚拟画笔当前横坐标，y坐标由参数指定）的垂直线。 |
| C | x1,y1 x2,y2 x,y | 曲线 | 从画笔当前点到x,y点绘制一条三次贝塞尔曲线。x1,y1和x2,y2是曲线的开始和结束控制点，控制其如何弯曲。 |
| S | x2,y2 x,y | 平滑曲线缩写 | 从画笔位置到点x,y绘制一条三次贝塞尔曲线。x2,y2为结束控制点。开始控制点与前一条曲线的结束控制点相同。 |
| Q | x1,y1 x,y | 二次贝塞尔曲线 | 从画笔当前坐标到x,y点绘制一条二次贝塞尔曲线。x1,y1是控制曲线如何弯曲的控制点。 |
| T | x,y | 平滑二次贝塞尔曲线缩写 | 从画笔位置到点x,y绘制一条三次贝塞尔曲线。控制点与所使用的的最后一个控制点相同。 |
| A | rx,ry x-axis-rotation large-arc-flag,sweep-flag x,y | 椭圆弧 | 从当前点到x,y点绘制一条椭圆弧。rx和ry为椭圆在x和y方向上的半径。x-rotation确定圆弧围绕x轴旋转的角度。当rx和ry的值不同时，它才会有效果。large-arc-flag表示是否是大圆弧，sweep-flag表示是否是顺时针绘制。 |
| Z |  | 闭合路径 | 通过从当前点到第一个点绘制一条线来关闭路径。 |



