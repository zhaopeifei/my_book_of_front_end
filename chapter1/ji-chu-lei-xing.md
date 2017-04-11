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

### 基本类型检测

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

###  {#number-}



