### 定义

**px：**绝对长度单位

**em：**相对长度单位

- 子元素字体大小的em是相对于父元素字体大小
- 元素的width/height/padding/margin用em的话是相对于该元素的font-size
- 参照物不一样

**rem：**相对长度单位，相对于html根元素的font-size属性，rem中的r意思是root（根源）

- 通常做法是给html元素设置一个字体大小，然后其他元素的长度单位就为rem。
- 参照物永远是根元素



### 背景

- em的出现是为了解决文字无法跟着页面一起放大这个问题。
- 如果需要多次修改被px设置的属性，会非常麻烦，所以可以使用rem/em来改变一处的font-size即可。



### **使用场景**

- 使用rem来设置font-szie，使用em来设置padding、margin、width、height、line-height等属性。
- 常用于响应式布局。

> 像素（px）：用于元素的边框或定位。
>
> em/rem：用于做响应式页面，不过我更倾向于rem，因为em不同元素的参照物不一样（都是该元素父元素），所以在计算的时候不方便，相比之下rem就只有一个参照物（html元素），这样计算起来更清晰。



### 示例

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



**计算：**

```js
<div>
    我是父元素div
    <p>
        我是子元素p
        <span>我是孙元素span</span>
    </p>
</div>

div {
  font-size: 40px;
  width: 10em; /* 400px */
  height: 10em;
  border: solid 1px black;
}
p {
  font-size: 0.5em; /* 20px */ 
  width: 10em; /* 200px */
  height: 10em;
  border: solid 1px red;
}
span {
  font-size: 0.5em; /* 10px */ 
  width: 10em; /* 100px */ 
  height: 10em;
  border: solid 1px blue;
  display: block;
}
```



参考文档：https://zhuanlan.zhihu.com/p/94369298
