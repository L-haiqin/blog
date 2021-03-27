block formatting context 块级格式化上下文

**作用：**

- 形成一个独立的渲染区域，内部元素的渲染不会影响到外界。

**形成BFC常见的条件：**

- 浮动元素：float不是none
- 绝对定位元素：position是absolute或者flexd
- 块级元素：overflow不是visible（前三种是最常见的）
- display：inline-block或者flex

**应用场景：**

- 清除浮动：给父级元素添加overflow:hidden，让父级元素拥有一个独立的渲染区域，里面的子元素随便怎么变化都不会影响到父元素。（利用了BFC这个原理）
- 很多网页会有广告的部分，需要用户叉掉广告，而没有影响到整个网页的布局，这就是用到了BFC的原理。通常会给这个广告设置absolute定位或者flexd定位。