# Undefined类型与Null类型

Undefined类型与Null类型都分别只有一个值：undefined、null。undefined表示“未定义”，而null表示“空值”。

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

