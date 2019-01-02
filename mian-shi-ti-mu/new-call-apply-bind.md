
# 手动实现call、apply、bind和new

## 手动实现call

```
// 函数原型上添加 myCall方法 来模拟call
Function.prototype.myCall = function(obj){
    //我们要让传入的obj成为, 函数调用时的this值.
    obj._fn_ = this;  //在obj上添加_fn_属性，值是this(要调用此方法的那个函数对象)。
    obj._fn_();       //在obj上调用函数,那函数的this值就是obj.
    delete obj._fn_; // 再删除obj的_fn_属性,去除影响.
    //_fn_ 只是个属性名 你可以随意起名，但是要注意可能会覆盖obj上本来就有的属性
}
```

