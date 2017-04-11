# 基本类型

ECMAScript语言中所有的值都有一个对应的语言类型，ECMAScript语言类型包括Undefined、Null、Boolean、String、Number和Object（ES6中新增了Symbol）。除了对象Object被称为复杂数据类型外，其他均被称为“基本类型”。

在ES中，值是有类型的，但是变量是没有类型的。所以说，ES不做“类型强制”，JS有时也因此被称为“弱类型语言”。例如，开始为一个变量a赋一个Number类型的值，以后也可以再为该变量赋一个String类型的值。

```
var a = 123; //此时a变量中的值为Number类型
a = '123'; //此时a变量中的值为String类型
```

### 内置类型 {#number-}

JavaScript有七种内置类型：

* 空值（null）
* 未定义（undefined）
* 布尔值（boolean）
* 数字（number）
* 字符串（string）
* 对象（object）
* 符号（symbol）

其中，前五种是ES5中的基础数据类型，称为“基本类型”。在ES6中，新增了符号（symbol）这种基本类型。上面七种数据类型中，除了对象（object）是“复杂类型”外，其他六种都是“基本类型”。

### 类型检测

---

通过typeof操作符可以查看值的类型。typeof返回的有：'undefined'、'boolean'、'string'、'number'、'object'、'function'、'symbol'。其中有个例外（bug），typeof null返回的值是‘object’。所以检测几种数据类型的方式为：

```
typeof undefined === 'undefined'; //true
typeof false     === 'boolean';   //true
typeof '123'     === 'string';    //true
typeof 123       === 'number';    //true
typeof Symbol()  === 'symbol';    //true

var a = null;
!a && typeof a === 'object'; //true，检查值是否为null

var obj = new Object();
obj && typeof obj === 'object'; //true
```

> 不同的对象在底层都表示为二进制，在JavaScript中二进制前三位都为0的话会被判断为object类型，null的二进制表示是全0，自然前三位也是0，所以执行typeof时会返回“object” ——你不知道的JS

### 基本类型与复杂类型区别 {#number-}

JavaScript不同于C++等语言，它没有指针。JavaScript对值和引用的赋值／传递在语法上没有区别，完全根据值的类型来决定。简单值（即标量基本类型值，scalar primitive）总是通过值复制的方式来赋值／传递；复合值（compound value）——对象（包括数组和其他封装对象等）和函数，则总是通过引用复制的方式来赋值／传递。

```
var a = 1;
var b = a;
b++;
a; // 1
b; // 2

var obj1 = { count: 1 };
var obj2 = obj1;
obj2.count++;
obj1.count; // 2
obj2.count; // 2
```

在调用函数时，会创建一份参数的副本，简单值复制值，复合值复制引用。

```
function foo(x) { // x是a的副本，由于a是复合值，所以x复制a的引用
    x.push(4);
    x; // [1, 2, 3, 4]
    
    x = [5, 6, 7]; // x指向另一个复合值
    x; // [5, 6, 7]
}

var a = [1, 2, 3];
foo(a);
a; //[1, 2, 3, 4]
```

我们在向函数foo传递a的时候，实际是将引用a的一个副本复制给x，而a仍指向\[1, 2, 3\]；在函数执行时，通过引用x来修改数组的值，因为此时a和x指向同一个值，所以x和a都指向了\[1, 2, 3, 4\]；但是\`x = \[5, 6, 7\]\`将x引用指向了另外一个数组\[5, 6, 7\]，而a的引用不被影响，所以，a仍然指向数组\[1, 2, 3, 4\]。也就是说，如果x是a引用副本，可以用x引用可以改变a引用共同指向的值，但是不能用x引用来修改a引用的指向。



