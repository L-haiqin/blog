### 1.1 基本标签
**html文档标签：**

`<!DOCTYPE>`、`<html>`、`<head>`、`<meta>`、`<title>`、`<link>`、`<style>`、`<body>`、`<script>`、`<noscript>`

**按闭合特征分类：**

 - 闭合标签：指由开始标签和结束标签组成的一对标签这种标签允许嵌套和承载内容，例如`<html></html>`、`<p></p>`等。
 - 空标签：指没有内容的标签，在开始标签中自动闭合。常见的空标签有：`<br>`、`<hr>`、 `<img>`、 `<input>`、 `<link>`、`<meta>`。

**按是否换行特征分类：**
- 块级元素：块级元素是值本身属性为`display:block`的元素。
- 内联元素：内联元素是指本身属性为`display:inline`的元素，其宽度随元素的内容而变化。

> 【补充】`display`属性
> 1、display 属性规定元素应该生成的框的类型
> 2、值：
> none：此元素不会被显示
> block：此元素将显示为块级元素，**此元素前后会带有换行符**
> inline：默认。此元素会被显示为内联元素，**元素前后没有换行符**
> inline-block：行内块元素

**块级元素的特点：**
- 每个块级元素独占一行，从上到下排布。
- 块级元素可以直接控制宽度高度等盒子模型相关的css属性。
- 不设置宽度的情况下，块级元素的宽度是其父级元素内容的宽度。
- 不设置高度的情况下，块级元素的高度是他本身内容的高度。

**常用块级元素：**
| 标签       | 描述                                             |
| ---------- | ------------------------------------------------ |
| div        | 常用块级容器，也是css和layout的主要标签          |
| h1-h6      | 一到六级标题                                     |
| hr         | 水平分割线                                       |
| menu       | 菜单列表                                         |
| ol、ul、li | 有序列表、无序列表、列表项                       |
| dl、dt、dd | 定义描述列表、定义项目/名字、描述每一个项目/名字 |
| table      | 表格                                             |
| p          | 段落                                             |
| form       | 表单                                             |

**内联元素的特点：**

- 内联元素会和其他元素从左到右显示在一行中。
- 内联元素不能直接控制宽度高度以及盒子模型相关的css属性，可以设置内外边距的水平方向的值。也就是说，对于内联元素的margin和padding，只有margin-left/margin-right和padding-left/padding-right是有效的，竖直方向的margin和pading无效果。
- 内联元素的宽高是由其内容本身的大小决定的。
- 内联元素只能容纳文本或者其他内联元素，不允许在内联元素中嵌套块级元素。

**常见的内联元素：**
| 标签   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| a      | 超链接（ href 属性）                                         |
| b      | 加粗                                                         |
| span   | 常用的内联容器，定义文本块                                   |
| img    | 图片                                                         |
| input  | 输入框                                                       |
| select | 创建单选或多选菜单，该元素是一种表单控件，可用于在表单中接受用户输入 |
| strong | 加粗强调                                                     |
| label  | 为 input 元素定义标注（标记）                                |

### 1.2 语义化标签
 `header` 、`section`、 `footer` 、`aside` 、`nav`、 `main`、`article`、`figure`、`hgroup`、`time`、`mark`、`address`

 例子：

```html
 <time datetime="2017-07-03">
```

```html
<header>
   <h1>信息科技有限公司</h1>
   <nav>
       <li><a href="#">首页</a></li>    
       <li><a href="aboutus_one.html">关于我们</a></li>
   </nav>
</header>
```
### 1.3 HTML5新特性

> H5 是符合 HTML5 的简称，HTML5 是一种标准，一种规范。
>
> 区别于SPA（single page web application，单页应用程序）： 是通过 HTML 与 Javscript 实现的单页应用程序，就是在一个页面上展示所有数据，通过 Javascript 去动态更新程序。
>
> 微信分享的那种中转页，小鹏汽车试驾预约的页面，属于H5？===》应该也可以用react或者html元素来写吧。

1）语义化标签

2）表单input，如color、date、datetime、email等

3）视频和音频，如audio、video、source、embed、track等

4）canvas绘图

5）svg绘图

6）Geolocation（地理定位）用于定位用户的位置

7）拖放api

8）Web Worker

Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行。

参考博客：[Web Worker 使用教程](http://www.ruanyifeng.com/blog/2018/07/web-worker.html)

9）Web Storage客户端存储数据

localStorage ：始终有效，窗口或浏览器关闭也一直保存；所有同源窗口都是共享的

sessionStorage ：针对一个 session 的数据存储, 当用户关闭浏览器窗口后，数据会被删除；不在不同的浏览器窗口共享，即使是同一个页面

cookie：只在设置的cookie过期时间之前一直有效，即使窗口和浏览器关闭；所有同源窗口都是共享的

常用API:

- 保存数据：localStorage.setItem(key,value);
- 读取数据：localStorage.getItem(key);
- 删除单个数据：localStorage.removeItem(key);
- 删除所有数据：localStorage.clear();
- 得到某个索引的key：localStorage.key(index);

10）WebSocket协议

HTTP协议只能发送单向请求，即只能客户端向服务端发送信息，然后服务端再返回给客户端，不能逆向进行；

而websocket可以实现，服务端主动向客户端推送信息，客户端也可以主动向服务端发送信息，是真正的双向信息交流。

协议标识符是ws（如果加密，则为wss）。

常用API：[添加链接描述](http://www.ruanyifeng.com/blog/2017/05/websocket.html)

参考博客：[HTML5的十大新特性](https://www.cnblogs.com/vicky1018/p/7705223.html)

### 1.4 布局
#### 1.4.1 flex布局（弹性布局）
主轴、交叉轴、水平、垂直

**flex容器的属性：**

1）**flex-direction**：定义主轴的方向

2）flex-wrap：换行

3）flex-flow：是flex-direction属性和flex-wrap属性的简写形式

4）**justify-content**：定义**flex项目**在主轴上的对齐方式（左/右对齐，居中，两端对齐等）

5）**align-items**：定义**flex项目**在交叉轴上的对齐方式

6）align-content

**flex项目的属性：**

1）order

2）flex-grow

3）flex-shrink

4）flex-basis

5）flex

6）align-self：允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性

参考博客：[Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)、[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

#### 1.4.2 盒模型
**盒模型属性：**

 - margin：外边距
 - padding：内边距
 - border：边框
 - content：内容

![img](https://img-blog.csdnimg.cn/20201020165516448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70#pic_center)



现在采用W3C盒模型作为标准的盒模型，即width指content部分的宽度。

> margin和padding的区别：
>
> **使用margin的场景：**
>
> - 需要在border（边框）以外的区域添加空白间距时；
> - 空白区域不需要使用背景颜色时
> - 注意：上下两个相连的盒子之间使用的外边距需要相互抵消。
> - 需要使用负值对页面布局时（注意：margin的值可以取负数，padding的值是不能使负数的）
>
>  margin在垂直方向上相邻的值相同时会发生叠加，水平方向的值会相加。margin取负值时，在垂直方向上，两个元素的边界仍然会重叠，但是，此时一个为正数，一个为负数，并不是取其中较大的值，而是用边界的绝对值，也就是正的边界值和负的边界值相加。
>
> 
>
> **使用padding的场景：**
>
> - 需要在border内侧添加空白
> - 空白区域不需要使用到背景颜色
> - 注意：上下相连的两个盒子之间的空白，间距是两者之和
>
> 
>
> 最后，**需注意margin是用来隔开元素与元素的间距；padding是用来隔开元素与内容的间距**；margin用于布局分开元素，使元素与元素互不干扰；padding用于元素与内容之间间隔，让内容与元素之间有一段距离。

#### 1.4.3 两栏布局

基本方法同三栏布局。

#### 1.4.4 三栏布局（圣杯布局/双飞翼布局）
左右固定，中间自适应。

1）网格布局（grid布局）

```css
display:grid;
grid-template-rows: 100px;
/*auto关键字表示由浏览器自己决定长度*/
grid-template-columns: 300px auto 300px; /*第二列的宽度，基本上等于该列单元格的最大宽度*/
```

参考博客：[CSS Grid 网格布局教程](http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html)

fr关键字、auto-fill关键字、minmax()、repeat()、auto关键字

2）浮动

三栏固定高度，左右固定宽度，并分别设置左右浮动

```css
.wrapper {
            margin: 0 100px; /*关键点*/
        }
        .conter,
        .left,
        .right {
            height: 100px;
        }
.conter {
            background-color: red;
            width: 100%;
            float: left; /*关键点*/
        }
        .left {
            width: 100px;
            background: green;
            float: left; /*关键点*/
            margin-left: -100px; /*关键点*/
        }
        .right {
            width: 100px;
            background: yellow;
            float: right; /*关键点*/
            margin-right: -100px; /*关键点*/
        }
```

3）flex布局

容器设置`display: flex;`，`justify-content: space-between;`；

三栏固定高度，左右固定宽度；

中间设置`flex:1;`；

flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。

4）position定位

5）表格布局

参考博客：[两栏布局和三栏布局](https://blog.csdn.net/qq_36634628/article/details/108656630?utm_medium=distribute.pc_relevant.none-task-blog-title-1&spm=1001.2101.3001.4242)、[两栏布局，三栏布局](https://www.cnblogs.com/jununx/p/3336553.html)

> **1、头部底部固定，中间自适应，且可以滚动？**
>
> 方法一：
>
> 使用[flex布局](https://so.csdn.net/so/search?q=flex布局&spm=1001.2101.3001.7020)，头部和底部的高度可以根据内容来决定，也可以自己设置。
>
> ```js
> .container {
>   height: 100vh; /* 这个值可以适当调整，不一定是100vh */
>   display: flex;
>   flex-direction: column;
> }
> 
> .header {}
> 
> .content {
>   flex-grow: 1;
>   overflow: auto;
> }
> 
> .footer {}
> ```
>
> 方法二：
>
> 使用绝对定位，将底部按钮固定在底部
>
> ```js
> .footer{
>   position:'absolute', // 绝对定位
>   bottom:0,
>   top:0,
>   left:0,
>   right:0
> }
> 
> .content {
>   flex: 1;
>   overflow: auto;
> }
> ```
>
> **2、左右元素固定，比如常见的导航栏，左边是面包屑，右边是个人头像**
>
> 

### 1.5 水平居中
1）行内元素（内联元素）

- 判断其父元素是不是块级元素，如果是，则直接给父元素设置 `text-align: center;` ；

- 如果不是，则将其父元素设置为块级元素，即        `display: block;`， `text-align: center;` 

2）块级元素

- 是否定宽
  - 给出具体宽度的：需要居中的标签设置 `margin: 0 auto;` 
  - 没有给出具体宽度的：将需要居中显示的子元素转换成行内元素，即`display: inline-block;` 或 `display: inline;`，再给父元素设置 `text-align: center;` 
- 使用**position定位属性**：先设置父元素为相对定位（relative），再设置子元素为绝对定位（absolute），设置子元素的`left:50%`
  - 定宽度：设置绝对子元素的 `margin-left: -元素宽度的一半px;` 或者`transform: translateX(-50%);`
  - 不定宽度：设置绝对子元素`transform: translateX(-50%);`
- **flex布局实现**：给待处理的块级元素的父元素添加属性 `display: flex; justify-content: center;`

### 1.6 垂直居中
1）单行的行内元素

- 设置单行行内元素的 "行高等于盒子的高" 即可

2）多行的行内元素
- 使用给父元素设置`display:table-cell;`和`vertical-align: middle;`属即可

3）块级元素
- 使用position定位：先设置父元素为相对定位，再设置子元素为绝对定位，设置子元素的`top: 50%`，即让子元素的左上角垂直居中
  - 定高度：设置绝对子元素的 `margin-top: -元素高度的一半px;` 或者`transform: translateY(-50%);`
  - 不定高度：设置绝对子元素`transform: translateY(-50%);`
- 使用flex布局实现：给待处理的块状元素的父元素添加属性 `display: flex; align-items: center;`

### 1.7 水平垂直居中
1）已知高度和宽度的元素
- 设置父元素为相对定位，给子元素设置绝对定位，给需要水平垂直居中的子元素设置`top: 0; right: 0; bottom: 0; left: 0; margin: auto;`，或者该子元素设置`left: 50%; top: 50%; margin-left: -该子元素宽度的一半px; margin-top: -该子元素高度的一半px;`

2）未知高度和宽度的元素

- 设置父元素为相对定位，给子元素设置绝对定位，给需要水平垂直居中的子元素设置`left: 50%; top: 50%; transform: translateX(-50%) translateY(-50%);`
- 设置父元素为flex定位，设置父元素`display: flex; justify-content: center; align-items: center;`

参考博客：[CSS水平居中+垂直居中+水平/垂直居中的方法总结](https://blog.csdn.net/weixin_37580235/article/details/82317240#%C2%A0%E5%9D%97%E7%BA%A7%E5%85%83%E7%B4%A0)

### 1.8 清除浮动的几种方式
为什么要清除浮动？

是为了解决父级元素因为子级浮动引起的内部高度为0的问题（父级变成一条线）；

边框不能随内容显示；

margin、padding没有效果；

影响其他兄弟元素。

#### 1.8.1 clear清除浮动
给谁清除浮动，就在其后面额外添加一个空白标签，对空白标签设置：`clear:both;`，这对后续元素（块级或者行内）产生的影响都将消失。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201020225223959.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201020225212344.png#pic_center)

#### 1.8.2 父级添加overflow
给父级元素添加`overflow:hidden;`。

#### 1.8.3 使用after伪元素
给父元素添加伪类。

```css
// 给父元素添加伪类，推荐此方法
.wrapper:after{
    content: "";
    clear:both;
    display: block;
    height: 0;
    overflow: hidden;
    visibility: hidden;
}
```

#### 1.8.4  使用before和after双伪元素

```css
/* wrapper为父元素 */
.wrapper:before,.wrapper:after{
    content:"";
    display:table;
}
.wrapper:after{
    clear:both;
}
.wrapper{
    *zoom:1; 
    /* zoom是IE专用属性，firefox等是不支持的。 */
    /* 设置zoom:1可以在IE6下清除浮动、解决margin导致的重叠等问题。 */
}
```

参考博客：[清除浮动的几种方法](https://www.cnblogs.com/plsmile/p/11125641.html)

### 1.9 三种常用的定位
**固定定位flexd：**
- 固定定位与绝对定位类似，但它是**相对于浏览器窗口定位，并且不会随着滚动条进行滚动**。

- 固定定位的最常见的一种用途是在页面中创建一个固定头部、固定脚部或者固定侧边栏，不需使用margin、border、padding。

**相对定位relative：**
- 相对于设置相对定位的该元素来进行定位，依据**left、right、top、bottom**等属性在正常文档流中偏移自身位置（以定位元素本身的位置为参考）
- 不会改变其他元素的布局，会在此元素本来位置留下空白

**绝对定位absolute：**
- 如果它的父元素设置了除static之外的定位，比如`position:relative`或`position:absolute`及`position:fixed;`，那么它就会相对于它的父元素来定位，**位置通过left , top ,right ,bottom属性来规定**；（以定位元素的父元素的位置为参考）

- 如果它的父元素没有设置定位，那么就得看它父元素的父元素是否有设置定位，如果还是没有就继续向更高层的祖先元素类推，总之它的定位就是**相对于设置了除static定位之外的定位的第一个祖先元素**，如果所有的祖先元素都没有以上三种定位中的其中一种定位，那么它就会相对于文档body来定位（并非相对于浏览器窗口，相对于窗口定位的是fixed）；
- 元素之间可层叠，会改变其他元素的布局，不会在此元素本来位置留下空白

**默认定位static：**
- 默认值，没有定位

**粘性定位sticky与继承定位inherit**



> **在我们的mr diff的页面上，怎么保证当前改动的文件可以固定在顶部，内容可以滚动？**
>
> 使用粘性定位`position: sticky;`，
>
> ```html
> <!DOCTYPE html>
> <html>
>   <head>
>     <style>
>       body {
>         margin: 0;
>         padding: 0;
>       }
> 
>       .header {
>         height: 50px;
>         background-color: #ccc;
>       }
> 
>       .diff-container {
>         /* display: flex; */
>         width: 200px;
>       }
> 
>       .file-list {
>         width: 200px;
>         background-color: #f0f0f0;
>         overflow-y: auto;
>         position: sticky;
>         top: 50px; /* 考虑到头部的高度 */
>       }
> 
>       .content {
>         flex: 1;
>         padding: 20px;
>         overflow-y: auto;
>       }
>     </style>
>   </head>
>   <body>
>     <div class="header">页面头部</div>
>     <div class="diff-container">
>       <div>
>         <div class="file-list">
>           <div>/projects/user.ts</div>
>         </div>
>         <div class="content">
>           <span>
>             【强制】必须遵守。是不得不遵守的约定，违反本约定或将会引起严重的后果。
>             【推荐】尽量遵守。长期遵守这样的规定，有助于系统稳定性和合作效率的提升。
>             【建议】充分理解。技术意识的引导，是个人学习、团队沟通、项目合作的方向。
>           </span>
>         </div>
>       </div>
>       <div>
>         <div class="file-list">
>           <div>/projects/user.ts</div>
>         </div>
>         <div class="content">
>           <span>
>             【强制】必须遵守。是不得不遵守的约定，违反本约定或将会引起严重的后果。
>             【推荐】尽量遵守。长期遵守这样的规定，有助于系统稳定性和合作效率的提升。
>             【建议】充分理解。技术意识的引导，是个人学习、团队沟通、项目合作的方向。
>           </span>
>         </div>
>       </div>
>       <div>
>         <div class="file-list">
>           <div>/projects/user.ts</div>
>         </div>
>         <div class="content">
>           <span>
>             【强制】必须遵守。是不得不遵守的约定，违反本约定或将会引起严重的后果。
>             【推荐】尽量遵守。长期遵守这样的规定，有助于系统稳定性和合作效率的提升。
>             【建议】充分理解。技术意识的引导，是个人学习、团队沟通、项目合作的方向。
>           </span>
>         </div>
>       </div>
>     </div>
>   </body>
> </html>
> ```
>
> 