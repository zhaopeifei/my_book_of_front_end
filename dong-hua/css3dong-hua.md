# CSS动画
目前，CSS动画主要包含两种方式：transition（过渡）和animation（动画）。

## CSS transition
>CSS Transitions allows property changes in CSS values to occur smoothly over a specified duration.

CSS transition允许css属性在一段时间内平稳地改变，进而能够实现过渡效果的动画。

### 属性设置
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

> transition-property: none | <single-transtion-property>#


