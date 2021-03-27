**px：**绝对长度单位

**em：**相对长度单位，相对于父元素的font-size属性（有问题，是相对于父元素还是自身font-size大小？？？？？？）

**rem：**相对长度单位，相对于html根元素的font-size属性

**为什么：**

- em的出现是为了解决文字无法跟着页面一起放大这个问题。
- 如果需要多次修改被px设置的属性，会非常麻烦，所以可以使用rem/em来改变一处的font-size即可。

**使用场景：**

- 使用rem来设置font-szie，使用em来设置padding、margin、width、height、line-height等属性。
- 常用于响应式布局。

**例子：**

```css
html{
  font-size:16px;
}
@media  (min-width: 400px){
  html{
    font-size:20px;
  }
}
.title{
  font-size:1rem;
}
.content{
  font-size:0.75rem;
  padding-top:0.5em; /* 通过自身的font-size来改变padding-top */
}
```

