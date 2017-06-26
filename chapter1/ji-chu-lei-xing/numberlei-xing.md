# Number类型 {#number-}

#### Number类型基础

JavaScript只有一种数值类型：number（数字），包括“整数”和带小数的十进制数。JavaScript没有真正意义上的整数，它的整数是没有小数的浮点数，即12.0等同于整数12。number类型使用IEEE754格式（64位双精度浮点数）来表示整数和浮点数值。为支持各种数值类型，ECMA-262定义了不同的数值字面量格式。

* 整数：基本的为十进制整数，数字前为0表示八进制，为0x表示十六进制整数。
* 浮点数值。可以用小数点表示，也可以用e表示，即科学表示法。
* 由于内存的限制，ECMAScript不能保存所有的数值。如果一个值超出了范围，会被转换为Infinity和-Infinity，表示正无穷和负无穷。
* NaN，即非数值是一个特殊的数值，用来表示一个本来要返回数值的操作数未返回数值的情况（这样就不会抛出错误）。任何涉及NaN的操作都会返回NaN，但NaN不等于任何值，包括其本身.要判断一个参数是不是数值可以用isNaN\(\)函数。

#### Number对象

number对象是经过封装的能让你处理数字值的对象，由 `Number()`构造器创建。如：`var num = new Number('123');`如果构造器中的参数无法被转换成数字，则返回NaN。如果没有`new`操作符，`Number()`能被用来执行类型转换。

number对象的数值有以下属性：

[`Number.EPSILON`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/EPSILON)：两个可表示\(representable\)数之间的最小间隔。

[`Number.MAX_SAFE_INTEGER`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER)：在 JavaScript 中最大的安全整数 \(2\)。

[`Number.MAX_VALUE`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_VALUE)

能表示的最大正数。最小的负数是

`-MAX_VALUE`

。

[`Number.MIN_VALUE`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/MIN_VALUE)

能表示的最小正数即最接近 0 的正数 \(实际上不会变成 0\)。最大的负数是

`-MIN_VALUE`

。

[`Number.NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/NaN)

特殊的“非数字”值。

[`Number.NEGATIVE_INFINITY`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/NEGATIVE_INFINITY)

特殊的负无穷大值，在溢出时返回该值。

[`Number.POSITIVE_INFINITY`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/POSITIVE_INFINITY)

特殊的正无穷大值，在溢出时返回改值。

#### Number数值

除此外，还有3个函数可以把非数值转换为数值：Number\(\)、parseInt\(\)、parseFloat\(\)，第一个可以用于任何数据类型，另外两个专门用于把字符串转换为数值。如果是Boolean，true和false分别转换为0和1；如果是null，返回0；如果是undefined，返回NaN；如果是对象，先调用对象的valueof\(\)，然后按照规则转换返回的值，如果返回的是NaN，再调用对象的toString\(\)方法，按照规则转换返回的字符串值；如果为字符串，规则如下：

```
var num = Number("123");       // 123
var num = Number("0123");      // 123，前导的零都会被忽略
var num = Number("0xf");       // 15，包含十六进制格式，转换为相同大小的十进制
var num = Number("");          // 0，为空字符串，则返回0
var num = Number("hello 123"); // NaN，如果字符串中包含以上之外的字符，转换为NaN。
```

在将字符串转换为Number时，一般使用parseInt\(\)/patseFloat\(\)

```
var num = parseInt("123blue");  // 123，
var num = parseInt("");         // NaN
var num = parseInt("070");      // 56，八进制
var num = parseInt("0xf");      // 15，十六进制
var num = parseInt("22.5");     // 22
var num = parseInt("AF",16);    // 175，按十六进制解析

var num = parseFloat("0908.5"); // 908.5
var num = parseFloat("3.15e7"); // 32150000
var num = parseFloat("0xf");    // 0
```





