#### css隐藏元素的方法

1、`overflow:hidden`

- overflow的hidden用来隐藏元素溢出部分，**占据空间，无法响应点击事件**。

2、`opacity:0`

- 元素依然存在原来的位置，**占据空间也可响应事件**。

3、`visibility:hidden`（常用）

- **占据空间，无法点击，**不会响应任何用户交互。

4、`display:none`（常用）

- 经典的display隐藏元素，这个是彻底的隐藏了元素，**不占据空间，也就不影响布局，当然也无法响应事件**。该元素以及它的所有后代元素都会隐藏。

5、`position:absolute`

```css
.hide{
    position：absolute; /* 绝对定位 */
    left:-99999px;
    top:-90999px;/* 不占据空间，无法点击 */
}
.hide-2{
    position：relative; /* 相对定位 */
    left:-99999px;
    top:-90999px;/* 占据空间，无法点击 */
}
```

- 假设有一个元素你想要与它交互，但是你又不想让它影响你的网页布局，没有合适的属性可以处理这种情况（opacity 和 visibility 影响布局， display 不影响布局但又无法直接交互）。
- 在这种情况下，考虑**将元素移出可视区域**，这个办法既不会影响布局，有能让元素保持可以操作。

6、`clip(clip-path):rect()/inset()/polygon()`（剪裁）

7、`z-index:-1000`（遮盖）

8、`transform:scaleY(0)`



#### display:none和visibility:hidden两者的区别

很多前端的同学认为`visibility: hidden`和`display: none`的区别仅仅在于`display: none`隐藏后的元素不占据任何空间，而`visibility: hidden`隐藏后的元素空间依旧保留 ，实际上没那么简单，visibility是一个非常有故事性的属性。

1、`visibility`具有继承性，给父元素设置`visibility:hidden;`，子元素也会继承这个属性。但是如果重新给子元素设置`visibility: visible`,则子元素又会显示出来。这个和display: none有着质的区别

2、`visibility: hidden`不会影响计数器的计数

3、CSS3的`transition`支持`visibility`属性，但是并不支持`display`，由于`transition`可以延迟执行，因此可以配合`visibility`使用纯css实现`hover`延时显示效果



参考博客：https://juejin.cn/post/6844903456545701901