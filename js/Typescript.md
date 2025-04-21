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

### 3 ts & js区别

|   **对比维度**   |                        **TypeScript**                        |                        **JavaScript**                        |
| :--------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|   **类型系统**   | **静态类型**，支持类型注解和编译时检查（如 `let age: number = 25`） | **动态类型**，变量类型在运行时确定（如 `let age = 25; age = "30"` 合法） |
|  **编译与运行**  |       需通过编译器（如 `tsc`）转译为 JS 代码后才能运行       |          直接由浏览器或 Node.js 解释执行，无需编译           |
|   **工具支持**   | 提供 IDE 智能提示、类型推断、重构支持（如 VS Code 深度集成） |     依赖外部工具（如 ESLint）进行代码检查，IDE 支持较弱      |
| **面向对象特性** |  支持接口、枚举、泛型、抽象类、访问修饰符等完整面向对象特性  |   ES6 引入类语法，但缺乏接口、泛型等高级特性，基于原型继承   |
|    **模块化**    |            原生支持模块化，可将代码封装为独立模块            |    依赖 ES6 模块语法，早期版本需通过库（如 CommonJS）实现    |
|   **错误处理**   |        编译时捕获类型错误（如类型不匹配、未定义变量）        |       错误通常在运行时暴露（如 `Uncaught TypeError`）        |
|   **适用场景**   | 大型项目、团队协作，需高可维护性和类型安全（如企业级应用、复杂前端框架） | 小型项目、快速原型开发、灵活性优先场景（如脚本工具、简单页面交互） |

都是面向对象的语言，js可以通过原型链、对象、类实现面向对象。

### 3 面向对象 & 面向过程

|  **对比维度**  | **面向过程**（Procedural Programming） | **面向对象**（Object-Oriented Programming，简称 OOP） |
| :------------: | :------------------------------------: | :---------------------------------------------------: |
|  **核心思想**  |           步骤分解与顺序执行           |                  对象协作与职责划分                   |
| **数据与行为** |        分离（函数操作外部数据）        |               封装（对象管理自身数据）                |
|   **复用性**   |           低（依赖函数复制）           |                   高（继承、多态）                    |
|  **典型应用**  |           算法实现、脚本工具           |                   大型系统、GUI开发                   |
| **设计复杂度** |          简单直接，但扩展性差          |              初期成本高，长期可维护性强               |

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

