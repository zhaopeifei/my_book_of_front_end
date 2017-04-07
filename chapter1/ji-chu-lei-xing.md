# 基本类型

ECMAScript语言中所有的值都有一个对应的语言类型，ECMAScript语言类型包括Undefined、Null、Boolean、String、Number和Object（ES6中新增了Symbol）。除了对象Object被称为复杂数据类型外，其他均被称为“基本类型”。

在ES中，值是有类型的，但是变量是没有类型的。所以说，ES不做“类型强制”，JS有时也因此被称为“弱类型语言”。例如，开始为一个变量a赋一个Number类型的值，以后也可以再为该变量赋一个String类型的值。

```
var a = 123; //此时a变量中的值为Number类型
a = '123'; //此时a变量中的值为String类型
```

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

### Undefined类型

Undefined类型只有一个值，即特殊的undefined，表示“未定义“。变量在未持有值的时候为undefined。但是undefined并不等同于undeclared（未声明）。浏览器在检测到未声明的变量时，会报错。但是typeof操作符无法分出undefined和undeclared，返回的值都是“undefined”。取未声明的变量的值时会报错，而typeof检查未声明的变量不会报错。

```
var a;

typeof a; // 'undefined'
typeof b; // 'undefined'

a; // undefined
b; // ReferenceError: b is not defined(其实此处应该是“b is not declared“更准确)
```

虽然typeof未声明的变量返回‘undefined’，但是这也并非是一种bug，而是一种安全机制。该安全防范机制对在浏览器中运行的js代码是有帮助的，因为多个脚本文件可能会在共享的全局命名空间中加载变量。比如，在程序中使用全局变量DEBUG作为“调试模式“的开关，该变量只在debug.js文件中声明，在其他脚本文件中，通过typeof检测该变量类型判断是否为调试模式。

```
if(DEBUG){ // 当该变量不存在时，会抛出引用错误
    console.log('debugging is starting');
} 

if(typeof DEBUG !== 'undefined'){ // 这样时安全的
    console.log('debugging is starting');
} 

if(window.DEBUG){ // 这样是安全的，但是不能运行在多种JS环境中（服务器node.js等），全局变量并非总是window
    console.log('debugging is starting');
} 
```

另外，可以使用void运算符来获取undefined。void \*返回undefined，一般使用`void 0`来获取undefined。

```
var a = 123;
void a; // undefined
void 0; // undefined
```

### Null类型

Null类型只有一个值，这个特殊值是null，表示空值。如果定义的变量准备在将来用于保存对象，最好初始化为null。注意，null和undefined都仅仅是一个值，如果我们错误地调用他们的属性或方法会出现类型错误TypeError。所以，在引用一个变量的属性和方法前，要保证他们不是null或者undefined。

### Boolean类型 {#boolean-}

该类型只有两个字面值：true、false。与数字值不一样，即true不一定等于1，false也不一定等于0.要将一个值转换为对应的boolean值，可用转型函数Boolean\(\)，至于返回的这个值是true还是false，取决于要转换值的数据类型及其实际值。注意：使用typeof返回的值为“boolean”，而不是“bool”。

### number类型 {#number-}

这种类型使用IEEE754格式来表示整数和浮点数值。为支持各种数值类型，ECMA-262定义了不同的数值字面量格式。

* 整数：基本的为十进制整数，数字前为0表示八进制，为0x表示十六进制整数。
* 浮点数值。可以用小数点表示，也可以用e表示，即科学表示法。
* 由于内存的显示，ECMAScript不能保存所有的数值。如果一个值超出了范围，会被转换为Infinity和-Infinity，表示正无穷和负无穷。
* NaN，即非数值是一个特殊的数值，用来表示一个本来要返回数值的操作数未返回数值的情况（这样就不会抛出错误）。任何涉及NaN的操作都会返回NaN，但NaN不等于任何值，包括其本身.要判断一个参数是不是数值可以用isNaN\(\)函数。

除此外，还有3个函数可以把非数值转换为数值：Number\(\)、parseInt\(\)、parseFloat\(\)，第一个可以用于任何数据类型，另外两个专门用于把字符串转换为数值。如果是Boolean，true和false分别转换为0和1；如果是null，返回0；如果是undefined，返回NaN；如果是对象，先调用对象的valueof\(\)，然后按照规则转换返回的值，如果返回的是NaN，再调用对象的toString\(\)方法，按照规则转换返回的字符串值；如果为字符串，规则如下：

```
var num = Number("123");       //123
var num = Number("0123");      //123，前导的零都会被忽略
var num = Number("0xf");       //15，包含十六进制格式，转换为相同大小的十进制
var num = Number("");          //0，为空字符串，则返回0
var num = Number("hello 123"); //NaN，如果字符串中包含以上之外的字符，转换为NaN。

```



