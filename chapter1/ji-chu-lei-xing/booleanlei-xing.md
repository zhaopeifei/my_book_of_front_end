# Boolean类型 {#boolean-}

该类型只有两个字面值：true、false。与数字值不一样，即true不一定等于1，false也不一定等于0.要将一个值转换为对应的boolean值，可用转型函数Boolean\(\)，至于返回的这个值是true还是false，取决于要转换值的数据类型及其实际值。注意：使用typeof返回的值为“boolean”，而不是“bool”。

```
typeof true; // "boolean"
true == 1; // true
true === 1; // false

var bool = new Boolean(1); // Boolean{ [[PrimitiveValue]]:true }
typeof bool; // "object"
bool == true; // true
bool === true; //false
bool.toString(); // "true"
bool.valueOf(); // true
```

通过Boolean可以构造一个布尔值的对象包装器，它有两种布尔值，true和false。当输入构造函数的参数时false、null、0、-0、""、undefined和NaN时，会返回false类型的布尔值；其他任何对象或者

```
new Boolean(); // Boolean{ [[PrimitiveValue]]:false }
new Boolean(false); // Boolean{ [[PrimitiveValue]]:false }
new Boolean(null); // Boolean{ [[PrimitiveValue]]:false }
new Boolean(""); // Boolean{ [[PrimitiveValue]]:false }
new Boolean(NaN); // Boolean{ [[PrimitiveValue]]:false }

// Falsy values in JavaScript are false, null, 0, "", undefined, and NaN.

```





