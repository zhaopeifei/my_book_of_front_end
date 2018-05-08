# SVG动画

在svg中实现动画的方式有多种，分别适合不同的场景。

## SVG动画元素

---

### 属性过渡动画——set

在svg中，可以通过set元素，来设置某二哥属性在一定时间内，过渡为某个值，从而实现动画

```
<rect x="10" y="10" height="100" width="100">
    <set
        attributeName="width"
        attributeType="XML"
        to="300"
        begin="5s"
    />
</rect>
```

如上，要为某元素添加动画，可在其元素内嵌套set元素。set元素有四个属性可以设置：

* attributeName：要修改的属性名称，也可以是css样式名称
* attributeType：XML或CSS，如果修改的XML属性，则为XML，如果是修改形状的CSS属性，则为CSS
* to：属性目标值
* begin：属性修改开始时间，后面可以带时间单位：h、min、s\(秒\)、ms\(毫秒\)

### 属性动画——animate

animate元素用来为SVG形状的属性添加动画。示例如下：

```
<circle cx="30" cy="30" r="25" style="stroke: none; fill: #0000ff;">
    <animate
        attributeName="cx"
        attributeType="XML"
        from="30"
        to="470"
        begin="0s"
        dur="5s"
        fill="remove"
        repeatCount="indefinite"
    />

</circle>
```

如上，在SVG形状内嵌套animate元素即可为SVG形状的属性或者CSS样式添加动画，animate有以下属性可以设置：

* attributeName：要修改的属性名称，也可以是css样式名称；

* attributeType：XML或CSS，如果修改的XML属性，则为XML，如果是修改形状的CSS属性，则为CSS
* from：属性起始值
* to：属性结束值
* begin：动画开始时间，后面可以带时间单位：h、min、s\(秒\)、ms\(毫秒\)
* to：动画结束时间，后面可以带时间单位：h、min、s\(秒\)、ms\(毫秒\)
* fill：
* repeatCount：重复次数，可以设置成一个数值或indefinite

此外，可以为一个SVG形状添加多段动画，并控制其先后顺序。示例如下：

```
<rect x="0" y="0" width="30" height="15"
      style="stroke: #ff0000; fill: none;">

    <animate id="one"
             attributeName="x" attributeType="XML"
             from="0" to="400"
             begin="0s" dur="10s" fill="freeze"
            />
    <animate
            attributeName="y" attributeType="XML"
            from="0" to="50"
            begin="one.end" dur="10s" fill="freeze"
            />
</rect>
```

### 转换动画——animateTransform

animate不能为SVG形状的transform属性设置动画，为其设置动画需要使用animateTransform元素，其属性大致与animate相同。示例如下：

```
<rect x="20" y="20" width="100" height="40"
    style="stroke: #ff00ff; fill:none;" >
  <animateTransform attributeName="transform"
                    type="rotate"
                    from="0 100 100" to="360 100 100"
                    begin="0s" dur="10s"
                    repeatCount="indefinite"
          />
</rect>
```

### 路径移动动画——animateMotion

animateMotion元素可以使SVG形状沿着路径的线路移动。示例如下：

```
<rect x="0" y="0" width="30" height="15" style="stroke: #ff0000; fill: none;">
    <animateMotion
            path="M10,50 q60,50 100,0 q60,-50 100,0"
            begin="0s" dur="10s" repeatCount="indefinite"
            rotate="auto"
            />
</rect>
```

## SVG动画关系

---

### 组合动画

不同animate元素针对不同的属性，会有动画叠加的效果。示例如下：

```
<rect x="10" y="10" width="40" height="20"
     style="stroke: #000000; fill: none;">
   <animate attributeName="x"
            attributeType="XML"
            from="10" to="400"
            begin="0s" dur="10s"
            repeatCount="indefinite"
           />
   <animate attributeName="y"
            attributeType="XML"
            from="10" to="100"
            begin="0s" dur="10s"
            fill="freeze"
            repeatCount="indefinite"
           />
</rect>
```

然后，animateTransform之间会有动画覆盖的效果，如果希望两个animateTransform之间组合，需要为animateTransform的additive设置为“sum”。示例如下：

```
<rect x="10" y="10" width="40" height="20"
      style="stroke: #000000; fill: none;">
    <animateTransform attributeName="transform" attributeType="XML"
                      type="scale"
                      from="1" to="3"
                      begin="0s" dur="10s"
                      repeatCount="indefinite"
            />
    <animateTransform attributeName="transform" attributeType="XML"
                      type="rotate"
                      from="0 30 20" to="360 30 20"
                      begin="0s" dur="10s"
                      fill="freeze"
                      repeatCount="indefinite"
                      additive="sum"
            />
</rect>
```



