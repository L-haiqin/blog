### 弹性盒布局

**Flexbox** 是 flexible box 的简称（注：意思是“灵活的盒子容器”），是 CSS3 引入的新的布局模式。

之所以被称为 Flexbox ，是因为它能够**扩展和收缩 flex 容器内的元素**，以最大限度地填充可用空间。与以前布局方式（如 table 布局和浮动元素内嵌块元素）相比，Flexbox 是一个更强大的方式：

- 在不同方向排列元素
- 重新排列元素的显示顺序
- 更改元素的对齐方式
- 动态地将元素装入容器

**什么情况下不建议使用 Flexbox ？**

虽然 Flexbox 非常适合缩放，对齐和重新排序元素，但以下情况应该尽量避免使用 Flexbox 布局：

- 整体页面布局
- 完全支持旧浏览器的网站

**三个核心概念：**

- flex 项（愚人码头注：也称 flex 子元素），需要布局的元素
- flex 容器，其包含 flex 项
- 排列方向（direction），这决定了 flex 项的布局方向（注：更多的文章叫主轴） 

**flex容器的属性：**

- **flex-direction**：定义主轴的方向

- flex-wrap：换行

- flex-flow：是flex-direction属性和flex-wrap属性的简写形式

- **justify-content**：定义**flex项目**在主轴上的对齐方式（左/右对齐，居中，两端对齐等）

- **align-items**：定义**flex项目**在交叉轴上的对齐方式

- align-content

**flex项目的属性：**

- order

- flex-grow

- flex-shrink

- flex-basis

- flex

- align-self：允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性

参考博客：[Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)、[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)、[什么是弹性（Flex）布局 ？15分钟弄懂Flex布局](https://zhuanlan.zhihu.com/p/139678377)