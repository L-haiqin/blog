### vue SPA首屏优化

设计模式，vue如何组件封装

#### 1、SSR服务端渲染

#### 2、按需引入

##### （1）路由懒加载

- require

```javascript
/* vue异步组件技术 */
{
  path: '/home',
  name: 'home',
  component: resolve => require(['@/components/home'],resolve)
},{
  path: '/index',
  name: 'Index',
  component: resolve => require(['@/components/index'],resolve)
}
```

- import

```javascript
// 下面2行代码，没有指定webpackChunkName，每个组件打包成一个js文件。
const Index = () => import('@/components/index')
const About = () => import('@/components/about') */
// 下面1行代码，指定了相同的webpackChunkName，会合并打包成一个js文件。 把组件按组分块
const Home =  () => import(/* webpackChunkName: 'ImportFuncDemo' */ '@/components/home')）
```

- require.ensure(dependencies: String[], callback: function(require), chunkName: String)  多个路由指定相同的chunkName，会合并打包成一个js文件。

```javascript
/* 组件懒加载方案三: webpack提供的require.ensure() */
{
  path: '/home',
  name: 'home',
  component: r => require.ensure([], () => r(require('@/components/home')), 'demo')
}, {
  path: '/index',
  name: 'Index',
  component: r => require.ensure([], () => r(require('@/components/index')), 'demo')
}
```

##### （2）ui库按需引入模块

```javascript
import {button} from 'element-ui';
```

#### 3、请求优化

##### （1）css，js放置位置

##### （2）使用字体图标

 将众多小图标合并到同一张图上 ，再根据 `background-position` 来设置图片的位置 。

#### 4、CDN内容分发网络

静态资源都上传到 CDN，可以极大地提高访问速度 。

#### 5、静态资源压缩，开启 GZIP 压缩

前端配置gzip压缩，并且服务端使用nginx开启gzip，用来减小网络传输的流量大小。配置好后，响应头会包含 `content-encoding:gzip`字段。

#### 6、webpack相关配置优化

##### （1）压缩js文件

使用`uglifyjs-webpack-plugin`插件代替webpack自带UglifyJsPlugin插件来压缩JS文件；生产环境关闭源码映射source-map，一方面能减少代码包的大小，另一方面也有利于系统代码安全；清除打印日志和debugger信息；配置SplitChunks 抽取公有代码，提升你的应用的性能

##### （2）压缩css文件

使用`mini-xss-extract-plugin`提取CSS 到单独的文件, 并使用`optimize-css-assets-webpack-plugin`来压缩CSS文件 。

##### （3）webpack-bundle-analyzer

webpack-bundle-analyzer是用来分析 Webpack 生成的包体组成并且以可视化的方式反馈给开发者的工具，可以通过命令:    

```
npm run build --report
```

来查看依赖关系，然后再根据具体情况划分代码块。

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/7/5/7d95caf73d8533c119519566b805ea64~tplv-t2oaga2asx-watermark.awebp)

 

#### 7、代码层面的优化

（1）合理使用v-if和v-show

（2）合理使用watch和computed

（3）使用v-for必须添加key, 最好为唯一id, 避免使用index, 且在同一个标签上，v-for不要和v-if同时使用

（4）定时器的销毁。可以在beforeDestroy()生命周期内执行销毁事件；也可以使用$once这个事件侦听器，在定义定时器事件的位置来清除定时器。

（5）防抖节流

（6）图片懒加载

#### 总结

**代码层面、项目打包、项目部署**方面的优化。

参考博客：[Vue SPA 首屏加载优化实践](https://juejin.cn/post/6844903519993118734#heading-0)、[『前端优化』—— Vue项目性能优化](https://juejin.cn/post/6857856269488193549#heading-0)、[揭秘 Vue.js 九个性能优化技巧](https://juejin.cn/post/6922641008106668045#heading-0)