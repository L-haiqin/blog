### 1 基础

React Native 组件就是对原生视图的封装。所以说为什么rn性能好一些，因为最终被渲染的其实都是原生组件，但是h5就是div这种元素，需要在WebView里面渲染。

React Native 渲染的是通用平台视图（宿主视图，ios，Android）而不是 DOM 节点（可以认为 DOM 是 Web 的宿主视图）。

在 React Native 0.71 版本之前，JSX 语法糖的实质是调用`React.createElement`方法，所以你必须在文件头部引用`import React from 'react'`。但在 React Native 0.71 版本之后，官方引入了[新的 JSX 转换](https://zh-hans.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html)，可以**不用**再在文件头部写`import React from 'react'`。

React Native 使用[Metro](https://metrobundler.dev/)构建 JavaScript 代码和资源。

**面试题：rn和h5的区别**

> 渲染方式和运行环境不一样：
>
> - rn会通过js桥接转换成ios/android原生组件进行渲染。
> - h5运行在浏览器或WebView，访问一些摄像头啥的需要依赖浏览器api的支持。
>
> 跨平台开发和维护成本：
>
> - rn代码复用率较高，但需针对平台差异编写少量原生代码。
> - H5一套代码适合所有平台，但需要处理不同浏览器的兼容问题。功能迭代受限于WebView性能瓶。
>
> 动画：
>
> - 对动画要求比较高可以用h5，css比较丰富。
> - rn的动画上手成本比较低，但是支持的动画样式不是很多。
>
> 内存：
>
> - RN通过原生组件管理内存更高效。
> - H5应用在低端设备上可能因WebView内存占用过高引发卡顿。
>
> 场景：
>
> - H5:
>   - **快速迭代的营销页面**：如活动页、表单收集，需频繁更新且无需复杂交互
>   - **内容展示型应用**：如新闻阅读、文档查看，对性能要求较低且需跨平台兼容
>   - **轻量级混合开发**：将H5嵌入原生应用的非核心页面（如帮助中心），降低开发成本
> - rn：
>   - **高性能需求的应用**：如社交类App（聊天、动态流）、电商类App（商品详情页），需流畅滚动和复杂动画
>   - **需深度集成设备功能**：如AR导航、实时音视频通话，依赖原生硬件加速

### 2 组件

ScrollView：滚动视图，全部渲染。支持手势翻页。

长列表：

- Flatlist：只渲染当前区域内的元素
  - 如果你的[`FlatList`](https://reactnative.cn/docs/flatlist)渲染得很慢, 请确保你使用了[`getItemLayout`](https://reactnative.cn/docs/flatlist#getitemlayout)，它通过跳过对items的处理来优化你的渲染速度。
  - [列表配置优化](https://reactnative.cn/docs/optimizing-flatlist-configuration)
- SectionList：分组

### 3 API

平台相关：使用Platform

- Platform.OS返回ios或者android

- Platform.select({ios:xxx, android:xxx})
- Platform.Version获取版本

手势系统：左右翻页/滑动，双指放大。touchable组件和[panresponder](https://reactnative.cn/docs/panresponder)

- touchable组件的onPress方法使用requestAnimationFrame

  - ```js 
    handleOnPress() {
      requestAnimationFrame(() => {
        this.doExpensiveAction();
      });
    }
    ```

  - 

动画Animation：https://reactnative.cn/docs/animations#requestanimationframe

- 修改图片尺寸建议使用transform: [{scale}]

网络：支持fetch和web socket

存储：async storage（类似web的local storage）

import异步调用改成require内联：https://reactnative.cn/docs/optimizing-javascript-loading

 [Turbo 原生模块](https://reactnative.cn/docs/turbo-native-modules-introduction) 是否有使用？？

### 4 渲染

Fabric 是 React Native 新架构的渲染系统。

渲染器使用三个不同的线程：

- UI 线程（主线程）：唯一可以操作宿主视图的线程。
- JavaScript 线程：这是执行 React 渲染阶段的地方。
- 后台线程：专门用于布局的线程。

[react native渲染](https://reactnative.cn/architecture/render-pipeline)组件的流程：

- 渲染（Render）：在 JavaScript 中，React 执行那些产品逻辑代码创建 [React 元素树（React Element Trees）](https://reactnative.cn/architecture/glossary#react-element-tree-and-react-element)。然后在 C++ 中，用 React 元素树创建 [React 影子树（React Shadow Tree）](https://reactnative.cn/architecture/glossary#react-shadow-tree-and-react-shadow-node)。
- 提交（Commit）：在 React 影子树完全创建后，渲染器会触发一次提交。这会将 React 元素树和新创建的 React 影子树的提升为“下一棵要挂载的树”。 这个过程中也包括了**布局信息计算**。
- 挂载（Mount）：React 影子树有了布局计算结果后，它会被转化为一个[宿主视图树（Host View Tree）](https://reactnative.cn/architecture/glossary#host-view-tree-and-host-view)。

![Step two](https://reactnative.cn/assets/images/render-pipeline-3-1dc3249f58a1ecef0392b7faabaca37c.png)