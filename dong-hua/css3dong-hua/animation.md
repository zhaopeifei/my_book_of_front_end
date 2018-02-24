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
<single-timing-function>等同于css transition中transition-timing-function的值，默认值为“ease”。

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
animation-direction指示动画是否反向播放。
```
animation-direction： <single-animation-direction> #
<single-animation-direction> = normal | reverse | alternate | alternate-reverse
```
* normal: 每个循环内动画向前循环，换言之，每个动画循环结束，动画重置到起点重新开始，这是默认属性。
* alternate: 动画交替反向运行，反向运行时，动画按步后退，同时，带时间功能的函数也反向，比如，ease-in 在反向时成为ease-out。计数取决于开始时是奇数迭代还是偶数迭代。
* reverse：反向运行动画，每周期结束动画由尾到头运行。
* alternate-reverse：反向交替， 反向开始交替。动画第一次运行时是反向的，然后下一次是正向，后面依次循环。决定奇数次或偶数次的计数从1开始。

### animation-fill-mode
animation-fill-mode用来指定在动画执行之前和之后如何给动画的目标应用样式。
```
animation-fill-mode: <single-animation-fill-mode>#
<single-animation-fill-mode> = none | forwards | backwards | both
```
* none: 动画执行前后不改变任何样式，默认值。
* forwards：目标保持动画最后一帧的样式。
* backwards：动画采用相应第一帧的样式。
* both：动画将会执行 forwards 和 backwards 执行的动作。

### animation-name
animation-name属性指定应用的一系列动画，每个名称代表一个由@keyframes定义的动画序列。
```
animation-name: [none | <keyframes-name>]#
```
默认值为none，表示无关键帧，会使动画失效。

### animation
```
animation: <single-animation> #
<single-animation> = <time> || <single-timing-function> || <time> || <single-animation-iteration-count> || <single-animation-direction> || <single-animation-fill-mode> || <single-animation-play-state> || [ none | <keyframes-name> ]
```

### @keyframe

```
@keyframes <keyframes-name> {
  <rule-list>
}

<keyframe-block> = <keyframe-selector># { <declaration-list> }
<keyframe-selector> = from | to | <percentage>
```

```
div {
  animation-name: slide-right;
  animation-duration: 2s;
}
@keyframes slide-right {
  from {
    margin-left: 0px;
  }
  50% {
    margin-left: 110px;
    opacity: 1;
  }
  50% {
     opacity: 0.9;
  }
  to {
    margin-left: 200px;
  }
}
```