### header固定，aside固定，main主体可以滚动

#### 需求

利用elementui的Container 布局容器，布置一个有顶部header，侧边栏aside以及主体main，现要求当主题长度过长时可以滚动，但是顶部和侧边固定。

> 类似于shark的用例列表页布局，[【css】左右拖拽布局](https://blog.csdn.net/weixin_43973415/article/details/127569144)

#### 思路

利用position中的absolute和relative，以及overflow-y来实现。

#### 详解

在elementui官网复制Container 布局容器布局，代码如下

```html
<el-container>
  <el-header>Header</el-header>
  <el-container>
    <el-aside width="200px">Aside</el-aside>
    <el-main>Main</el-main>
  </el-container>
</el-container>
```

1. **为el-header添加样式**

```css
.el-header {
        position: relative;
        width: 100%;
        height: 60px;      
    }
```

官网el-header默认高度就是为60px；这里为了定位对比，声明一下。

2. **为el-aside添加样式**

```css
.el-aside {
  display: block;
  position: absolute;
  left: 0;
  top: 60px;
  bottom: 0;
  }
```

3. **为el-main添加样式**

```css
.el-main {
  position: absolute;
  left: 200px;
  right: 0;
  top: 60px;
  bottom: 0;
  overflow-y: scroll; /* 显示滚动条 */
  }
```

这里主要是relative和absolute，el-header设置为relative，其余两个设置为absolute，相当于el-header 的relative为父级，absolute以它为基准进行位置调整，top：60px就是距离顶部60px，因为el-header高度为60px，el-main的left：200px，因为el-aside默认宽度为200px，这样一来就能完美契合。

`overflow-y `决定如何处理溢出的内容。隐藏溢出内容（hidden），或者显示滚动条（scroll），或者直接显示溢出内容（visible），或者让浏览器来处理（auto）。

另一种方法：[element侧栏固定，header固定，利用ref](https://blog.csdn.net/hotqin888/article/details/88755918)

#### 类似快手面试题目

手写css题，一个竖向滚动列表，header固定，中间content自适应 如果内容多要滚动（一开始没太明白啥意思，后面给了个min-height的解法，面试官提示了一下flex） 

方法一：[【css】头部底部固定，中间出现滚动条](https://blog.csdn.net/weixin_43973415/article/details/127569060)

```js
.container {
  height: 100vh; /* 这个值可以适当调整，不一定是100vh */
  display: flex;
  flex-direction: column;
}

.header {}

.content {
  flex-grow: 1;
  overflow: auto;
}

.footer {}
```

方法二：使用absolute绝对定位，`bottom:0`

使用绝对定位来处理：`flex: 1` 和 `position: relative;` 二选一即可。

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      body {
        /* margin: 0;
        padding: 0;
        position: relative; */
        flex: 1;
      }

      .header {
        height: 50px;
        background-color: #ccc;
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
      }

      .content {
        padding-top: 50px; /* 考虑到固定头部的高度 */
        padding-bottom: 50px; /* 考虑到固定底部的高度 */
        overflow-y: auto; /* 添加滚动条 */
        flex: 1;
      }

      .footer {
        height: 50px;
        background-color: #ccc;
        position: absolute;
        bottom: 0;
        left: 0;
        right: 0;
      }
    </style>
  </head>
  <body>
    <div class="header">头部内容</div>
    <div class="content">
      <!-- 中间自适应内容 -->
      <!-- 这里可以放置您的页面内容 -->
    </div>
    <div class="footer">底部内容</div>
  </body>
</html>
```

#### 类似滴滴面试题目

让页面的header有80px的高度固定在顶部，左边也固定一个元素，右边自适应（写出来） 

-  为什么页面上会有空白（因为body自己有一个margin数值）（通配margin，padding） 
- 是什么起作用（所有标签的都会匹配，为了性能不会使用通配，而是会使用枚举所有可能会用的到的标签）     
- 手写了一个顶部和左边固定，右边自适应的布局     
- 最后给顶部下面又加了一个粘滞定位的固定高度的顶部

通过position定位实现。

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      body {
        margin: 0;
        padding: 0;
        /* position: relative; */
      }

      .header {
        height: 80px;
        background-color: #ccc;
        position: absolute; /* 或者fixed */
        top: 0;
        left: 0;
        right: 0;
      }

      .sidebar {
        width: 200px;
        background-color: #f0f0f0;
        position: absolute; /* 或者fixed */
        top: 80px; /* 考虑到头部的高度 */
        bottom: 0;
      }

      .content {
        margin-left: 200px; /* 考虑到侧边栏的宽度 */
        margin-top: 80px; /* 考虑到头部的高度 */
        padding: 20px;
        height: 500px;
        overflow: scroll;
      }
    </style>
  </head>
  <body>
    <div class="header">页面头部</div>
    <div class="sidebar">左侧导航栏</div>
    <div class="content">
      <span>
        【强制】必须遵守。是不得不遵守的约定，违反本约定或将会引起严重的后果。
        【推荐】尽量遵守。长期遵守这样的规定，有助于系统稳定性和合作效率的提升。
        【建议】充分理解。技术意识的引导，是个人学习、团队沟通、项目合作的方向。
        【强制】必须遵守。是不得不遵守的约定，违反本约定或将会引起严重的后果。
        【推荐】尽量遵守。长期遵守这样的规定，有助于系统稳定性和合作效率的提升。
        【建议】充分理解。技术意识的引导，是个人学习、团队沟通、项目合作的方向。
      </span>
    </div>
  </body>
</html>
```

