### css继承属性

#### 一、有继承性的属性

**1、字体系列属性**

font：组合字体

font-family：规定元素的字体系列

font-weight：设置字体的粗细

font-size：设置字体的尺寸

font-style：定义字体的风格

font-variant：设置小型大写字母的字体显示文本，这意味着所有的小写字母均会被转换为大写，但是所有使用小型大写字体的字母与其余文本相比，其字体尺寸更小。

font-stretch：允许你使文字变宽或变窄。所有主流浏览器都不支持。

font-size-adjust：为某个元素规定一个 aspect 值，字体的小写字母 "x"的高度与"font-size" 高度之间的比率被称为一个字体的 aspect 值。这样就可以保持首选字体的 x-height。

**2、文本系列属性**

text-indent：文本缩进

text-align：文本水平对齐

text-shadow：设置文本阴影

line-height：行高

word-spacing：增加或减少单词间的空白（即字间隔）

letter-spacing：增加或减少字符间的空白（字符间距）

text-transform：控制文本大小写

direction：规定文本的书写方向

color：文本颜色

**3、元素可见性**：visibility

**4、表格布局属性**

caption-side

border-collapse

empty-cells

**5、列表属性**

list-style-type

list-style-image

list-style-position、list-style

**6、设置嵌套引用的引号类型**：quotes

**7、光标属性**：cursor

**8、不常用的**：speak，page等属性

#### 二、所有元素可以继承的属性

1、元素可见性：visibility

2、光标属性：cursor

#### 三、内联元素可以继承的属性

1、字体系列属性

2、除text-indent、text-align之外的文本系列属性

#### 四、块级元素可以继承的属性

text-indent、text-align

#### 五、无继承的属性

1、display

2、文本属性

vertical-align

text-decoration

3、盒子模型的属性：宽度、高度、内外边距、边框等

4、背景属性：背景图片、颜色、位置等

5、定位属性：浮动、清除浮动、定位position等

6、生成内容属性：content、counter-reset、counter-increment

7、轮廓样式属性：outline-style、outline-width、outline-color、outline

8、页面样式属性：size、page-break-before、page-break-after

#### 六、继承中比较特殊的几点

1、a 标签的字体颜色不能被继承

1、`<h1>-<h6>`标签字体的大小是不能被继承的，因为它们都有一个默认值

