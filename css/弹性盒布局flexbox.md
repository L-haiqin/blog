### 弹性盒布局

#### 概念

**Flexbox** 是 flexible box 的简称（注：意思是“灵活的盒子容器”），是 CSS3 引入的新的布局模式。

之所以被称为 Flexbox ，是因为它能够**扩展和收缩 flex 容器内的元素**，以最大限度地填充可用空间。与以前布局方式（如 table 布局和浮动元素内嵌块元素）相比，Flexbox 是一个更强大的方式：

- 在不同方向排列元素
- 重新排列元素的显示顺序
- 更改元素的对齐方式
- 动态地将元素装入容器

#### 什么情况下不建议使用 Flexbox ？

虽然 Flexbox 非常适合缩放，对齐和重新排序元素，但以下情况应该尽量避免使用 Flexbox 布局：

- 整体页面布局
- 完全支持旧浏览器的网站

#### 三个核心概念

- flex 项（愚人码头注：也称 flex 子元素），需要布局的元素
- flex 容器，其包含 flex 项
- 排列方向（direction），这决定了 flex 项的布局方向（注：更多的文章叫主轴） 

#### flex容器的属性

- **flex-direction**：定义主轴的方向

- flex-wrap：换行

- flex-flow：是flex-direction属性和flex-wrap属性的简写形式

- **justify-content**：定义**flex项目**在主轴上的对齐方式（左/右对齐，居中，两端对齐等）

- **align-items**：定义**flex项目**在交叉轴上的对齐方式

- align-content

#### flex项目的属性

- order

- flex-grow：定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。

- flex-shrink：定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

- flex-basis：定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。它可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间。

- flex

- align-self：允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性

#### flex举例

[flex:0 flex:1 flex:auto flex:none之间的区别](https://blog.csdn.net/mozhu1997/article/details/109023858)

flex是`flex-grow`、`flex-shrink`、`flex-basis`的缩写。

##### flex: 1 

- flex: 1 1 0px

- 元素可以弹性增大也可以弹性减小。
- 较长的尺寸优先牺牲自己的尺寸 

##### flex: auto

- flex: 1 1 auto

- 元素可以弹性增大也可以弹性减小。
- 较长的尺寸优先扩展自己的尺寸 

##### flex: 0

- flex: 0 0 0%

- 元素不可以弹性增大也可以弹性减小。
- 元素最终的尺寸为最小宽度 

##### flex: none

- flex: 0 0 auto

- 元素不可以弹性增大也可以弹性减小。
- 元素内的内容不会换行 

参考博客：[Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)、[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)、[什么是弹性（Flex）布局 ？15分钟弄懂Flex布局](https://zhuanlan.zhihu.com/p/139678377)

### 标准盒模型和怪异盒模型

**盒模型属性：**

 - margin：外边距
 - padding：内边距
 - border：边框
 - content：内容

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201020165516448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70#pic_center)



现在采用W3C盒模型作为标准的盒模型，即width指content部分的宽度。

当设置为`box-sizing:content-box`时，将采用标准模式解析计算，也是默认模式；当设置为`box-sizing:border-box`时，将采用怪异模式解析计算 。



标准盒模型：盒子宽度=width(content)+padding+border

怪异盒模型：盒子宽度=width(包含padding,content,border)