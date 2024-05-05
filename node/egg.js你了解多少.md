### egg.js你了解多少

#### 1、概念

 egg 是阿里出品的一款 node.js 后端 web 框架，基于 koa 封装，并做了一些约定。 

#### 2、特点

Egg 的插件机制有很高的可扩展性，**一个插件只做一件事**（比如 [Nunjucks](https://mozilla.github.io/nunjucks) 模板封装成了 [egg-view-nunjucks](https://github.com/eggjs/egg-view-nunjucks)、MySQL 数据库封装成了 [egg-mysql](https://github.com/eggjs/egg-mysql)）。Egg 通过框架聚合这些插件，并根据自己的业务场景定制配置，这样应用的开发成本就变得很低。

Egg 奉行『**约定优于配置**』，按照[一套统一的约定](https://eggjs.org/zh-cn/advanced/loader.html)进行应用开发。

主要特点如下：

- 提供基于 Egg [定制上层框架](https://eggjs.org/zh-cn/advanced/framework.html)的能力
- 高度可扩展的[插件机制](https://eggjs.org/zh-cn/basics/plugin.html)
- 内置[多进程管理](https://eggjs.org/zh-cn/advanced/cluster-client.html)
- 基于 [Koa](http://koajs.com/) 开发，性能优异
- 框架稳定，测试覆盖率高
- [渐进式开发](https://eggjs.org/zh-cn/tutorials/progressive.html)

#### 3、区别

 [Express](http://expressjs.com/) 是 Node.js 社区广泛使用的框架，简单且扩展性强，非常适合做个人项目。但框架本身缺少约定，标准的 MVC 模型会有各种千奇百怪的写法。Egg 按照约定进行开发，奉行『约定优于配置』，团队协作成本低。 

##### express与koa：

###### **（1）中间件**

Koa 的中间件和 Express 不同，Koa 选择了**洋葱圈模型**。 

###### **（2）context**

和 Express 只有 Request 和 Response 两个对象不同，Koa 增加了一个 **Context 的对象**，作为这次请求的上下文对象（在 Koa 1 中为中间件的 `this`，在 Koa 2 中作为中间件的第一个参数传入）。

###### **（3）异常处理**

通过**同步方式编写异步代码**带来的另外一个非常大的好处就是异常处理非常自然，使用 `try catch` 就可以将按照规范编写的代码中的所有错误都捕获到。这样我们可以很便捷的编写一个自定义的错误处理中间件。

```javascript
async function onerror(ctx, next) {
  try {
    await next();
  } catch (err) {
    ctx.app.emit('error', err);
    ctx.body = 'server error';
    ctx.status = err.status || 500;
  }
}
```

只需要将这个中间件放在其他中间件之前，就可以捕获它们所有的同步或者异步代码中抛出的异常了。

##### koa与egg：

Egg 选择了 Koa 作为其基础框架，在它的模型基础上，进一步对它进行了一些增强。 

###### **（1）扩展**

在基于 Egg 的框架或者应用中，我们可以通过定义 `app/extend/{application,context,request,response}.js` 来扩展 Koa 中对应的四个对象的原型，通过这个功能，我们可以快速的增加更多的辅助方法，例如我们在 `app/extend/context.js` 中写入下列代码：

```javascript
// app/extend/context.js
module.exports = {
  get isIOS() {
    const iosReg = /iphone|ipad|ipod/i;
    return iosReg.test(this.get('user-agent'));
  },
};
```

在 Controller 中，我们就可以使用到刚才定义的这个便捷属性了：

```javascript
// app/controller/home.js
exports.handler = ctx => {
  ctx.body = ctx.isIOS
    ? 'Your operating system is iOS.'
    : 'Your operating system is not iOS.';
};
```

更多关于扩展的内容，请查看[扩展](https://eggjs.org/zh-cn/basics/extend.html)章节。

###### **（2）插件**

众所周知，在 Express 和 Koa 中，经常会引入许许多多的中间件来提供各种各样的功能，例如引入 [koa-session](https://github.com/koajs/session) 提供 Session 的支持，引入 [koa-bodyparser](https://github.com/koajs/bodyparser) 来解析请求 body。而 Egg 提供了一个更加强大的插件机制，让这些独立领域的功能模块可以更加容易编写。

一个插件可以包含

- extend：扩展基础对象的上下文，提供各种工具类、属性。
- middleware：增加一个或多个中间件，提供请求的前置、后置处理逻辑。
- config：配置各个环境下插件自身的默认配置项。
- service

一个独立领域下的插件实现，可以在代码维护性非常高的情况下实现非常完善的功能，而插件也支持配置各个环境下的默认（最佳）配置，让我们使用插件的时候几乎可以不需要修改配置项。

 一个插件其实就是一个『迷你的应用』，包含了 [Service](https://link.juejin.cn/?target=https%3A%2F%2Feggjs.org%2Fzh-cn%2Fbasics%2Fservice.html)、[中间件](https://link.juejin.cn/?target=https%3A%2F%2Feggjs.org%2Fzh-cn%2Fbasics%2Fmiddleware.html)、[配置](https://link.juejin.cn/?target=https%3A%2F%2Feggjs.org%2Fzh-cn%2Fbasics%2Fconfig.html)、[框架扩展](https://link.juejin.cn/?target=https%3A%2F%2Feggjs.org%2Fzh-cn%2Fbasics%2Fextend.html)等，但是**没有独立的 [Router](https://link.juejin.cn/?target=https%3A%2F%2Feggjs.org%2Fzh-cn%2Fbasics%2Frouter.html) 和 [Controller](https://link.juejin.cn/?target=https%3A%2F%2Feggjs.org%2Fzh-cn%2Fbasics%2Fcontroller.html)**，也不能定义自己的 `plugin.js`。 

在应用或框架的 `config/plugin.js` 中声明：

```js
exports.myPlugin = {
  enable: true, // 是否开启
  package: 'egg-myPlugin', // 从 node_modules 中引入
  path: path.join(__dirname, '../lib/plugin/egg-mysql'), // 从本地目录中引入
  env: ['local', 'unittest', 'prod'], // 只有在指定运行环境才能开启
}
```

开启插件后，就可以使用插件提供的功能了：

```js
app.myPlugin.xxx()
```

如果插件包含需要用户自定义的配置，可以在 `config.default.js` 进行指定，例如：

```js
exports.myPlugin = {
  hello: 'world'
}
```

#### 4、目录结构

```javascript
egg-project
├── package.json
├── app.js (可选)
├── agent.js (可选)
├── app/
|   ├── router.js # 用于配置 URL 路由规则
│   ├── controller/ # 用于存放控制器（解析用户的输入、加工处理、返回结果）
│   ├── model/ (可选) # 用于存放数据库模型
│   ├── service/ (可选) # 用于编写业务逻辑层
│   ├── middleware/ (可选) # 用于编写中间件
│   ├── schedule/ (可选) # 用于设置定时任务
│   ├── public/ (可选) # 用于放置静态资源
│   ├── view/ (可选) # 用于放置模板文件
│   └── extend/ (可选) # 用于框架的扩展
│       ├── helper.js (可选)
│       ├── request.js (可选)
│       ├── response.js (可选)
│       ├── context.js (可选)
│       ├── application.js (可选)
│       └── agent.js (可选)
├── config/
|   ├── plugin.js # 用于配置需要加载的插件
|   ├── config.{env}.js # 用于编写配置文件（env 可以是 default,prod,test,local,unittest）
```

#### 5、路由（Router）

定义了 **请求路径（URL）** 和 **控制器（Controller）** 之间的映射关系 。

```javascript
module.exports = app => {
  const { router, controller } = app
  // 当用户访问 news 会交由 controller/news.js 的 index 方法进行处理
  router.get('/news', controller.news.index)
  // 通过冒号 `:x` 来捕获 URL 中的命名参数 x，放入 ctx.params.x
  router.get('/user/:id/:name', controller.user.info)
  // 通过自定义正则来捕获 URL 中的分组参数，放入 ctx.params 中
  router.get(/^\/package\/([\w-.]+\/[\w-.]+)$/, controller.package.detail)
}
```

#### 6、控制器（Controller）

负责**解析用户的输入，处理后返回相应的结果** 。

通常情况下，在 Controller 中会做如下几件事情：

- 接收、校验、处理 HTTP 请求参数
- 向下调用服务（Service）处理业务
- 通过 HTTP 将结果响应给用户

一个真实的案例如下：

```js
const { Controller } = require('egg');
class PostController extends Controller {
  async create() {
    const { ctx, service } = this;
    const createRule = {
      title: { type: 'string' },
      content: { type: 'string' },
    };
    // 校验和组装参数
    ctx.validate(createRule);
    const data = Object.assign(ctx.request.body, { author: ctx.session.userId });
    // 调用 Service 进行业务处理
    const res = await service.post.create(data);
    // 响应客户端数据
    ctx.body = { id: res.id };
    ctx.status = 201;
  }
}
module.exports = PostController;
```

 在 Controller 中通过 `this.ctx` 可以获取上下文对象 ，比如：

- `ctx.query`：URL 中的请求参数（忽略重复 key）
- `ctx.request.body`：HTTP 请求体中的内容
- `ctx.body`：设置响应体
- `ctx.getFileStream()`：获取上传的文件流

#### 7、服务（Service）

Service 是懒加载的，即只有当访问到它的时候框架才会去实例化它。 

通常情况下，在 Service 中会做如下几件事情：

- 处理复杂业务逻辑
- 调用数据库或第三方服务（例如 GitHub 信息获取等）

#### 8、中间件（Middleware）

在 `app/middleware` 目录下编写中间件，再 在 `config.default.js` 中配置：

```javascript
//  middleware/slow.js 慢查询中间件
module.exports = (options, app) => {
  return async function (ctx, next) {
    const startTime = Date.now()
    await next()
    const consume = Date.now() - startTime
    const { threshold = 0 } = options || {}
    if (consume > threshold) {
      console.log(`${ctx.url}请求耗时${consume}毫秒`)
    }
  }
}

// config.default.js
module.exports = {
  // 配置需要的中间件，数组顺序即为中间件的加载顺序
  middleware: [ 'slow' ],
  // slow 中间件的 options 参数
  slow: {
    enable: true
  },
}
```



参考博客：[学习egg.js，看这一篇就够了！](https://juejin.cn/post/6995063516470198279#heading-0)、[官方文档](https://eggjs.org/zh-cn/intro/index.html)

