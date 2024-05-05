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

> 通过绝对定位来隐藏元素，比如年度账单中，需要下载的内容view就放在可视区域外，`top:window_height`
>
> 如果是当用户点击下载按钮的时候，再渲染，可能会存在渲染来不及的情况，视图内容部分缺失。
>
> ```js
> shot: {
>   width: WINDOW_WIDTH,
>   height: WRAP_UP_HEIGHT,
>   position: 'absolute', // here
>   top: WRAP_UP_HEIGHT, // here
>   zIndex: -1,
> }
> ```
>
> 

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

- 不占据空间，无法点击

- rn当中，经常会遇到多个可点击区域重叠，不能点击到我们需要的那个按钮。

  > 这个时候应该不能用阻止默认事件的方式了，因为位于最下层的按钮根本不会被触发。
  >
  > 可能需要考虑布局

8、`transform:scaleY(0)`：*占据空间，无法点击*

> 年度账单中动画的实现：Animated结合translateX实现。
>
> ```js
> const animatedValue = React.useRef(new Animated.Value(0)).current; // 创建value
> 
> <View style={styles.contentContainer}>
>   {contents?.map((item, index) => {
>     // 0: ltr, 1: rtl, 2: ltr...
>     const imageDirection = index % 2 ? 1 : -1;
>     const animStart = 400 * index;
>     const animEnd = 600 + 400 * index;
>     const imageOffsetX = animatedValue.interpolate({
>       inputRange: [0, animStart, animEnd],
>       outputRange: [
>         imageDirection * WINDOW_WIDTH,
>         imageDirection * WINDOW_WIDTH,
>         0,
>       ],
>       // FIXME: 这里线性会有更好性能, 需要和设计对齐
>       easing: Easing.bezier(0.42, 0, 0.58, 1),
>     });
>     return (
>       <Animated.Image
>         source={{ uri: item.url }}
>         style={[
>           styles.img,
>           { width, transform: [{ translateX: imageOffsetX }] }, // here，会达到卡片从左向右滑出的效果
>         ]}
>         resizeMode="contain"
>       />
>     );
>   })}
> </View>
> ```
>
> 

#### display:none和visibility:hidden两者的区别

很多前端的同学认为`visibility: hidden`和`display: none`的区别仅仅在于`display: none`隐藏后的元素不占据任何空间，而`visibility: hidden`隐藏后的元素空间依旧保留 ，实际上没那么简单，visibility是一个非常有故事性的属性。

1、`visibility`具有继承性，给父元素设置`visibility:hidden;`，子元素也会继承这个属性。但是如果重新给子元素设置`visibility: visible`,则子元素又会显示出来。这个和display: none有着本质的区别

2、`visibility: hidden`不会影响计数器的计数

3、CSS3的`transition`支持`visibility`属性，但是并不支持`display`，由于`transition`可以延迟执行，因此可以配合`visibility`使用纯css实现`hover`延时显示效果



#### 例子

1、年度账单中，眼睛为什么不可以点击，怎么解决的？

**引申出来的问题：**RN ScrollView实现轮播图，希望左右点击，可以实现前后翻页，怎么做？

方案一：左右两边分别加一个TouchableOpacity，但是可能会存在多个TouchableOpacity重叠，导致位于底下的TouchableOpacity不生效的问题

方案二：使用手势系统[PanResponder](https://reactnative.cn/docs/panresponder)，比如像滑动答题的时候，就可以使用PanResponder

方案三：使用[ScrollView](https://reactnative.cn/docs/scrollview)的onTouchEnd事件

```js
const onTouchEnd = ((e: GestureResponderEvent) => {
  const locationX = e.nativeEvent.pageX;
  const pressLeft = locationX > 0 && locationX < WINDOW_WIDTH / 4;
  const pressRight =
    locationX > (WINDOW_WIDTH / 4) * 3 && locationX < WINDOW_WIDTH;
  if (scrollEnabled.current && (pressLeft || pressRight)) {
    if (pressLeft) {
      carouselInnerRef?.current?.gotoPage(activeIndex - 1, true); // 跳转到下一页
      indicatorProgressNumberRef.current = activeIndex - 1;
    }
    if (pressRight) {
      carouselInnerRef?.current?.gotoPage(activeIndex + 1, true); // 跳转到上一页
      indicatorProgressNumberRef.current = activeIndex + 1;
    }
  }
};
```



参考博客：https://juejin.cn/post/6844903456545701901