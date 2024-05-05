## href

Hypertext Reference 超文本引用

**使用 href 属性的标签：**

① a 超链接 :

```html
<a href="http://www.baidu.com" target="_blank">百度</a>
```

② link 链接 : 引用外部 css

```html
<link type="text/css" href="iknow_base.css" rel="stylesheet" />
```
- 用`rel`正向关系 属性 表示被 引用的文档是做什么的.
- 链接外部样式表,一般用 link- `href`引用url 属性 这样,比较符合 外部链接资源的特征，表示 不是内嵌的内容。

**浏览器如何处理：**

不必须属性，会让当前标签能够**链接到其它资源，不会替换掉标签本身的内容**。比如，

- `a` 超链接: `href` 引用url ，不会替换 标签自己的内容，而是 **点击后的跳转**

- `link` 链接: 是空标签，没有内容

## src

source 资源

**使用 src 属性的标签：**

① img 图片:

```html
<img src="/img/1.png" alt="1" />
```

② style 样式:

```
<style src="common.css"> </style>
```

③ scirpt 脚本 :

```
<script type="text/javascript" src="org.js"></script>
```

④ input-type=image 图片 提交按钮 标签:

```html
<input type="image" src="submit.jpg" alt="Submit" align="right" />
```

⑤ iframe 内联框架:

```
<iframe src="girl.gif"></iframe>
```

⑥ frame 框架:

```
<frame src="frame_a.htm" />
```

**浏览器如何处理：**

必须属性，**引入一个资源，将标签本身的内容整体替换**。不设置 `src`属性，标签本身会没有内容，`src` 不能缺少，必需的，比如，

- `script` 脚本，不存在脚本代码
- `img` 图片，不显示图片，会显示x
- `iframe` 内联框架，会显示空白页

参考博客：[【HTML 中 src 和 href 属性的区别】](https://blog.csdn.net/VickyTsai/article/details/89850448)