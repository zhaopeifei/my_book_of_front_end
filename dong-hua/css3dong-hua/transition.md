# CSS transition
>CSS Transitions allows property changes in CSS values to occur smoothly over a specified duration.

CSS transition允许css属性在一段时间内平稳地改变，进而能够实现过渡效果的动画。可应用于所有元素、::before和::after伪元素。

## 属性设置
css transition可以通过transition属性来设置
```
transition: <property> <duration> <timing-function> <delay>

/** 也可以分开写 **/
transition-property: [property]
transtion-duration: [duration]
transition-timing-function: [timing-function]
transition-delay: [delay]

/** demo **/
div {
  width: 100px;
  transition: width 3s ease-in 0.5s;
}
div:hover {
  width: 200px;
}
```

### transtion-property
transtion-property表示要过渡性改变的属性。

```
transition-property: none | <single-transtion-property> # /* #表示可重复一次或多次*/
<single-transtion-property> = all | <custom-ident>
```
transition-property默认值为all，不是继承属性，可以应用于所有元素、::before和::after伪元素，transition-property可以有多个值（如：width、height等）。

### transition-duration
transition-duration表示属性过渡性变化持续的时长。
```
transition-duration: <time> #
```
transition-duration默认值为"0s"，不是继承属性，可以应用于所有元素、::before和::after伪元素，transition-duration可以有多个值。

### transition-timing-function
transition-timing-function表示属性过渡性变化的变化速度。
```
transition-timing-function: <single-timing-function> #
<single-timing-function> = linear | <cubic-bezier-timing-function> | <step-timing-function> | <frames-timing-function>
<cubic-bezier-timing-function> = ease | ease-in | ease-out | ease-in-out | cubic-bezier(<number>, <number>, <number>, <number>)
<step-timing-function> = step-start | step-end | steps(<integer>[, [ start | end ] ]?)
<frames-timing-function> = frames(<integer>)
```
默认值为“ease”，非继承属性。

### transition-delay
transition-delay表示过渡性变化发生的时间，即延迟时间。
```
transition-delay: <time> #
```
transition-delay默认值为"0s"，不是继承属性，可以应用于所有元素、::before和::after伪元素，transition-delay可以有多个值。

### transition
transition是以上四个属性的简写，建议使用改写法。
```
transition: <single-transition> #
<single-transition> = [ none | <single-transition-property> ] || <time> || <single-timing-function> || <time>
```




