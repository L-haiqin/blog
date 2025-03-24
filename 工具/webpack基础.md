#### 定义

`webpack` 是一个现代 `JavaScript` 应用程序的静态模块打包器，当 `webpack` 处理应用程序时，会递归构建一个依赖关系图，其中包含应用程序需要的每个模块，然后将这些模块打包成一个或多个 `bundle`。 

#### 入口（entry）

- 单个入口语法

用法：`entry: string | [string]`

- 对象语法

 用法：`entry: {  string | [string] } | {}` 

用于描述入口的对象的属性：dependOn、filename、import、library、runtime、publicPath

#### 输出（output）

webpack 在哪里输出它所创建的 *bundle*，以及如何命名这些文件 。主要输出文件的默认值是 `./dist/main.js`，其他生成文件默认放置在 `./dist` 文件夹中。 

分发代码（存放在`./dist`文件夹中）是指在构建过程中，经过最小化和优化后产生的输出结果，最终将在浏览器中加载。 

#### 转换（loader）

webpack 只能理解 **JavaScript 和 JSON 文件**，这是 webpack 开箱可用的自带能力。**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 [模块](https://webpack.docschina.org/concepts/modules)，以供应用程序使用，以及被添加到依赖图中。 

在 webpack 的配置中，**loader** 有两个属性：

1. `test` 属性，识别出哪些文件会被转换。
2. `use` 属性，定义出在进行转换时，应该使用哪个 loader。

```javascript
const path = require('path');

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          // [style-loader](/loaders/style-loader)
          { loader: 'style-loader' },
          // [css-loader](/loaders/css-loader)
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          },
          // [sass-loader](/loaders/sass-loader)
          { loader: 'sass-loader' }
        ]
      }
    ]
  }
};
```

- 首先需要先npm相应的`xxx-loader`模块。
- 注意： 要定义在 `module.rules` 而不是 `rules` 中 。
- 链式调用：从右到左（从下往上）， 从 sass-loader 开始执行，然后继续执行 css-loader，最后以 style-loader 为结束。 
-  loader 可以通过 `options` 对象配置 。
- 除了常见的通过 `package.json` 的 `main` 来将一个 npm 模块导出为 loader，还可以在 module.rules 中使用 `loader` 字段直接引用一个模块。
- loader 可以是同步的，也可以是异步的。

两种使用 loader 的方式：

- [配置方式](https://webpack.docschina.org/concepts/loaders/#configuration)（推荐）：在 **webpack.config.js** 文件中指定 loader。
- [内联方式](https://webpack.docschina.org/concepts/loaders/#inline)：在每个 `import` 语句中显式指定 loader。

#### 插件（plugin）

用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。 

webpack **插件**是一个具有 [`apply`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 方法的 JavaScript 对象。`apply` 方法会被 webpack compiler 调用，并且在 **整个** 编译生命周期都可以访问 compiler 对象。 

想要使用一个插件，你只需要 `require()` 它，然后把它添加到 `plugins` 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建一个插件实例。 

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const webpack = require('webpack'); // 用于访问内置插件

module.exports = {
  module: {
    rules: [{ test: /\.txt$/, use: 'raw-loader' }],
  },
  plugins: [new HtmlWebpackPlugin({ template: './src/index.html' })],
};
```

-   `require()`  -> 添加到 `plugins` 数组中 -> `new`一个插件实例
- 在上面的示例中，`html-webpack-plugin` 为应用程序生成一个 HTML 文件，并自动注入所有生成的 bundle。 

#### 模式（mode）

通过选择 `development`, `production` 或 `none` 之中的一个，来设置 `mode` 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 `production`。 

#### 模块（modules）

常见的模块类型：

- [ES2015 `import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 和 `export`语句
- [CommonJS](http://www.commonjs.org/specs/modules/1.0/) `require()` 语句
- [AMD](https://github.com/amdjs/amdjs-api/blob/master/AMD.md) `define` 和 `require` 语句
- css/sass/less 文件中的 [`@import` 语句](https://developer.mozilla.org/en-US/docs/Web/CSS/@import)。
- stylesheet `url(...)` 或者 HTML `  <img src=...>  ` 文件中的图片链接。

 通过 **loader** 可以使 webpack 支持多种语言和预处理器语法编写的模块。 

 webpack 社区已经为各种流行的语言和预处理器创建了 *loader* 。

#### 模块解析（Module Resolution）

#### 模块热替换（hot module replacement，HMR）

模块热替换（HMR）功能会在应用程序运行过程中，替换、添加或删除 [模块](https://webpack.docschina.org/concepts/modules/)，而无需重新加载整个页面。 

#### 开发服务器（devServer）

  `webpack-dev-server` (简写: `dev-server`) 

#### 总结

[webpack-指南](https://webpack.docschina.org/guides/)：从0开始webpack配置

- `./dist` 文件夹用于存放webpack打包后的文件，最终可以在浏览器中加载展示。
- webpack.config.js文件里面进行相应loader的配置，便可以解析和加载css、csv、txt等不同格式的文件。
- ` npm run build `

一些api方法：[配置](https://webpack.docschina.org/configuration/)、[API](https://webpack.docschina.org/api/)

其它：

- 将JS代码向低版本转换，我们需要使用 `babel-loader`。 
- `webpack-dev-server`  
- `html-webpack-plugin`  
- `devtool` 中的一些设置，可以帮助我们将编译后的代码映射回原始源代码。 ` source-map `

参考博客：[带你深度解锁Webpack系列(基础篇)](https://juejin.cn/post/6844904079219490830#heading-0)

#### 1、Loader和Plugin的区别

`Loader` 本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。 因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，对其他类型的资源进行转译的预处理工作。

`Plugin` 就是插件，基于事件流框架 `Tapable`，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

`Loader` 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。

`Plugin` 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

#### 2、Webpack构建流程

- **初始化：**启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler。

- **编译：**从 Entry 出发，针对每个 Module 串行调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理。

- **输出：**将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中。

#### 3、模块打包原理

Webpack 实际上为每个模块创造了一个可以**导出和导入的环境**，本质上并没有修改代码的执行逻辑，代码执行顺序与模块加载顺序也完全一致。

#### 4、热更新原理

#### 5、webpack跨域

（1）使用代理：服务端是别人的，使用这个方式

```javascript
devServer:{
    proxy:{  // 重写的方式，把请求代理到express服务器上
        '/api':{
            target:'http://localhost:3000',
            pathRewrite:{'/api':''} // 把/api 替换为空
        }
    }
},
```

将前端以`/api`开头的请求代理到指定的服务端地址`http://localhost:3000`,并且会把请求地址的`/api`去除。

webpack proxy ，其基本行为是接受客户端发送的请求后转发给其他的服务器，目的是为了便于开发者在开发的模式下解决跨域的问题。

要想实现代理必须要一个中间服务器， webpack 提供服务器的工具是 `webpack-dev-server`，只适用于开发阶段。

可以在webpack 配置对象属性中通过 devServer 属性来配置。

（2）前端模拟数据

```javascript
devServer:{
    // 模拟数据
    before(app){
        app.get('/user',(req,res)=>{
            res.json({name:'小白'})
        })
    }
},
```

（3）服务端是自己写的，就可以把前端代码启动到服务端上

```javascript
// server.js
let express = require('express');
let app = express();
let webpack = require('webpack');

let middle = require('webpack-dev-middleware'); // 引入这个
let config = require('./webpack.config.js');    // 配置文件
let compiler = webpack(config);

app.use(middle(compiler))

app.get('/user',(req,res)=>{
    res.json({name:'小白2'})
})
app.listen(3001)
```

安装`webpack-dev-middleware`，用于在服务端启动webpack。

#### 6、如何对bundle体积进行监控和分析？

- `webpack-bundle-analyzer`：可视化分析模块体积
- `speed-measure-webpack-plugin` ：监控构建速度

#### 7、如何减少bundle大小

- 代码分割：将代码分割成多个chunk，按需加载。比如可以配置多个entry，提取公共模块。
- Tree Shaking：删除没有使用的文件代码。
- 资源压缩：使用一些loader压缩js和css、图片资源。
- 按需加载第三方库：比如可以只加载`import('moment/locale/zh-cn')`，使用cdn的方式导入第三方库。

#### 8、文件指纹

文件指纹是打包后输出的文件名的后缀。

`Hash`：和整个项目的构建相关，只要项目文件有修改，整个项目构建的 hash 值就会更改

`Chunkhash`：和 Webpack 打包的 chunk 有关，不同的 entry 会生出不同的 chunkhash

`Contenthash`：根据文件内容来定义 hash，文件内容不变，则 contenthash 不变






#### 存在的问题！！！！

**跟着官方文档或者掘金敲一遍！！！！！**

#### 参考博客

[「吐血整理」再来一打Webpack面试题](https://juejin.cn/post/6844904094281236487#heading-19)

[2021年前端面试必读文章【超三百篇文章/赠复习导图】](https://juejin.cn/post/6844904116339261447#heading-21)



