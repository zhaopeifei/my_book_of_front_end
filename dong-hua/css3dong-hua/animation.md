# css animation
>This CSS module describes a way for authors to animate the values of CSS properties over time, using keyframes. The behavior of these keyframe animations can be controlled by specifying their duration, number of repeats, and repeating behavior.

css animation允许用户使用关键帧去控制css的属性变化，其可控制的属性与css transition一致，css animation拥有比css transition更强的动画能力。可应用于所有元素、::before和::after伪元素。

## 属性设置
css animation可以通过animation属性和@keyframe来设置
```
div:hover {
  animation: 1s rainbow;
}

@keyframes rainbow {
  0% { background: #c00 }
  50% { background: orange }
  100% { background: yellowgreen }
}
```

### animation-duration
animation-duration表示动画持续时间，单位是秒
```
animation-duration: <time> # /* #表示可重复 */
```
默认值为"0s"，非继承属性，可以应用于所有元素、::before和::after伪元素，可以有多个值。


### animation-timing-function
animation-timing-function表示动画改变的属性的变化速度。
```
animation-timing-function: <single-timing-function> #
```
<single-timing-function>等同于css transition中transition-timing-function的值。

### animation-delay
```
animation-delay: <time> #
```
默认值为"0s"，非继承属性，可以应用于所有元素、::before和::after伪元素，可以有多个值。


### animation-iteration-count
animation-iteration-count定义动画在结束前运行的次数，可以是1次，也可以是无限次。
```
animation-iteration-count: <single-animation-iteration-count> #
<single-animation-iteration-count> = infinite | <number>
```
默认值为1，非继承属性。

### animation-direction

### animation-fill-mode

### animation-name


### animation
```
animation: <single-animation> #
<single-animation> = <time> || <single-timing-function> || <time> || <single-animation-iteration-count> || <single-animation-direction> || <single-animation-fill-mode> || <single-animation-play-state> || [ none | <keyframes-name> ]
```

