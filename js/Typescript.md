### 1 定义

TypeScript（ TS）是一种强类型、面向对象的编译语言。

### 2 优势

- 支持类和模块。支持接口、类、范型的定义

- 静态类型检查、类型断言、类型判断

- ES6 功能支持

- 清除库 API 定义。

- 对 JavaScript 打包的内置支持。

- JavaScript 的超集

[Typescript使用指南](https://blog.csdn.net/weixin_43973415/article/details/145028655)

### 3 面向对象 & 面向过程

**面向对象：**

- 对数据和操作进行封装，比如类、继承
- 封装、继承、多态

```js
// 定义一个简单的动物类
class Animal {
  constructor(name) {
    this.name = name;
  }

  // 定义一个方法
  makeSound() {
    return 'Animal sound';
  }
}

// 创建 Animal 类的实例
const animal = new Animal('Generic');

// 调用实例方法
console.log(animal.makeSound()); // 输出：Animal sound

// 定义子类 Dog，继承自 Animal
class Dog extends Animal {
  constructor(name, breed) {
    super(name); // 调用父类的构造函数
    this.breed = breed;
  }

  // 重写父类方法
  makeSound() {
    return 'Woof!';
  }

  // 定义子类特有的方法
  fetch() {
    return `${this.name} fetch!`;
  }
}

// 创建 Dog 类的实例
const dog = new Dog('旺财', 'husky');

// 调用继承自父类的方法
console.log(dog.makeSound()); // 输出：Woof!

// 调用子类特有的方法
console.log(dog.fetch()); // 输出：旺财 fetch!
```

**面向过程：**

- 只对操作进行封装
- 函数操作数据，可以将数据进行传参，而不是封装在对象内部

```js
// 计算圆的面积
function calculateArea(radius) {
    return Math.PI * radius * radius;
}

// 计算圆的周长
function calculatePerimeter(radius) {
    return 2 * Math.PI * radius;
}

// 调用函数计算结果
const radius = 5;
const area = calculateArea(radius);
const perimeter = calculatePerimeter(radius);

console.log("半径为", radius, "的圆的面积为:", area);
console.log("半径为", radius, "的圆的周长为:", perimeter);
```

面向过程和面向对象本质上的区别是两种不同的关于解决问题方法的思考方向不同。面向对象相比面向过程最大的优势在于不仅内聚了**逻辑**而且还内聚了**数据**，而面向过程只是内聚了逻辑。不过这个缺陷也是可以通过函数柯里化来进行一定的弥补。