### css链接样式

####  四种链接状态 

（1）`a:link`：设置链接的初始样式，也就是未被点击的样式。

（2）`a:visited`：设置链接被点击后的样式。

（3）`a:hover`：设置当鼠标悬浮于链接之上的样式。

（4）`a:active`：设置当链接被激活时的状态，也就是鼠标按键在链接上按下且未松开时的状态。

先后顺序：`:link > :visited > :hover > :active`。

#### 例子

```css
/* 未被访问的链接 */
a:link {
  color: red;
}

/* 已被访问的链接 */
a:visited {
  color: green;
}

/* 将鼠标悬停在链接上 */
a:hover {
  color: hotpink;
}

/* 被选择的链接 */
a:active {
  color: blue;
}
```

补充：

- text-decoration 属性主要用于从链接中删除下划线。


