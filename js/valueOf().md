### valueOf()

`valueOf()` 方法返回指定对象的原始值。

不同类型对象的valueOf()方法的返回值：

| **对象** | **返回值**                                               |
| -------- | -------------------------------------------------------- |
| Array    | 返回数组对象本身。                                       |
| Boolean  | 布尔值。                                                 |
| Date     | 存储的时间是从 1970 年 1 月 1 日午夜开始计的毫秒数 UTC。 |
| Function | 函数本身。                                               |
| Number   | 数字值。                                                 |
| Object   | 对象本身。这是默认情况。                                 |
| String   | 字符串值。                                               |
|          | Math 和 Error 对象没有 valueOf 方法。                    |

例子：

```javascript
// Array：返回数组对象本身
var array = ["ABC", true, 12, -5];
console.log(array.valueOf() === array);   // true

// Date：当前时间距1970年1月1日午夜的毫秒数
var date = new Date(2013, 7, 18, 23, 11, 59, 230);
console.log(date.valueOf());   // 1376838719230

// Number：返回数字值
var num =  15.26540;
console.log(num.valueOf());   // 15.2654

// 布尔：返回布尔值true或false
var bool = true;
console.log(bool.valueOf() === bool);   // true

// new一个Boolean对象
var newBool = new Boolean(true);
// valueOf()返回的是true，两者的值相等
console.log(newBool.valueOf() == newBool);   // true
// 但是不全等，两者类型不相等，前者是boolean类型，后者是object类型
console.log(newBool.valueOf() === newBool);   // false

// Function：返回函数本身
function foo(){}
console.log( foo.valueOf() === foo );   // true
var foo2 =  new Function("x", "y", "return x + y;");
console.log( foo2.valueOf() );
/*
ƒ anonymous(x,y
) {
return x + y;
}
*/

// Object：返回对象本身
var obj = {name: "张三", age: 18};
console.log( obj.valueOf() === obj );   // true

// String：返回字符串值
var str = "http://www.xyz.com";
console.log( str.valueOf() === str );   // true

// new一个字符串对象
var str2 = new String("http://www.xyz.com");
// 两者的值相等，但不全等，因为类型不同，前者为string类型，后者为object类型
console.log( str2.valueOf() === str2 );   // false
```

参考博客：[Object.prototype.valueOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf)

### toString()

`toString()` 方法返回一个表示该对象的字符串。

每个对象都有一个 `toString()` 方法。

默认情况下，`toString()` 方法被每个 `Object` 对象继承。如果此方法在自定义对象中未被覆盖，`toString()` 返回 "[object *type*]"，其中 `type` 是对象的类型。

可以通过 `toString()` 来获取每个对象的类型。为了每个对象都能通过 `Object.prototype.toString()` 来检测，需要以 `Function.prototype.call()` 或者 `Function.prototype.apply()` 的形式来调用，传递要检查的对象作为第一个参数，称为 `thisArg`。

例子：

```javascript
var toString = Object.prototype.toString;

toString.call(new Date); // [object Date]
toString.call(new String); // [object String]
toString.call(Math); // [object Math]

//Since JavaScript 1.8.5
toString.call(undefined); // [object Undefined]
toString.call(null); // [object Null]
```

### (a== 1 && a ==2 && a==3)可能为true吗?

#### 准备

`==`先转换类型再比较。

当使用 `==` 时，如果两个参数的类型不一样，那么 JS 会尝试将其中一个的类型转换为和另一个相同。

**valueOf和toString**这两个方法是每个对象都自带的(继承自Object原型)。**通过重写原生对象的`toString`或`valueOf`方法来实现。**

#### 实现

##### 方法一：重写toString()

重新定义一下变量a，重写它的toString方法：

```javascript
let a = {
  i: 1,
  toString () {
    return a.i++
  }
}

if(a == 1 && a == 2 && a == 3) {
  console.log('Hello World!');
}
// 输出Hello World!
```

执行`a==1`时，js引擎会尝试把对象类型a转化为数字类型，首先调用a的**valueOf**方法来判断，不行则继续调用**toString**方法，然后再把toString返回的**字符串转化为数字类型**再去和a作比较(这里我重写了toString就直接返回的数字类型的结果，正常情况toString返回的字符串)。

当使用 `==` 时，如果两个参数的类型不一样，那么 JS 会尝试将其中一个的类型转换为和另一个相同。在这里左边对象，右边数字的情况下，会首**先尝试调用 valueOf（如果可以调用的话）来将对象转换为数字，如果失败，再调用 toString**。

##### 方法二：重写valueOf()

转换时会先调用valueOf()再调用toString()。

```javascript
let a = {
  i: 1,
  valueOf () {
    return a.i++
  }
}

if(a == 1 && a == 2 && a == 3) {
  console.log('Hello World!');
}
// 输出Hello World!
```

更多方法：https://stackoverflow.com/questions/48270127/can-a-1-a-2-a-3-ever-evaluate-to-true

有意思的评论：[JavaScript (a ==1 && a== 2 && a==3) 可能为 true 吗？](https://zhuanlan.zhihu.com/p/33029291)

