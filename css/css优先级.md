### css优先级

优先级是基于不同种类**选择器**组成的匹配规则。

优先级就是分配给指定的 CSS 声明的一个**权重**，它由 匹配的选择器中的 每一种选择器类型的 数值 决定。

当CSS选择器权重相同，则最后的声明的CSS选择器覆盖靠前的 CSS。

#### **选择器类型的优先级**（以下递增的）：

- 类型选择器（例如，`h1`）和伪元素（例如，`::before`）

- 类选择器(例如，`.example`)，属性选择器（例如，`[type="radio"]`）和伪类（例如，`:hover`）

- ID 选择器例如，`#example`）。

**通配选择符**（universal selector）（[`*`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Universal_selectors)）**关系选择符**（combinators）（[`+`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Adjacent_sibling_combinator), [`>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Child_combinator), [`~`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/General_sibling_combinator), ['` `'](https://developer.mozilla.org/en-US/docs/Web/CSS/Descendant_combinator), [`||`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Column_combinator)）和 **否定伪类**（negation pseudo-class）（[`:not()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:not)，`:is()`，`:where`）对优先级没有影响，影响优先级的是括号里面的选择器。（但是，在  `:not()` 括号内声明的选择器会影响优先级）。需要注意的是，只有逻辑组合伪类的优先级是 0，其他伪类的优先级并不是这样的。 

#### 优先级

（外部样式）`External style sheet` **<**（内部样式）`Internal style sheet` **<**（内联样式）`Inline style`

#### **权重等级**

- 第一等：`!important`，引用：[【css】css优先级之 !important](https://blog.csdn.net/weixin_43973415/article/details/126510060)
- 第二等：代表内联样式，如: `style=""`，权值为1000。
- 第三等：代表ID选择器，如：`#content`，权值为100。
- 第四等：代表类，伪类和属性选择器，如 `.content`，权值为10。
- 第五等：代表元素和伪元素选择器，如 `h2`、`:before` 与 `:after`，权值为1。
- 第六等：通配选择器、选择符和逻辑组合伪类，权值为 0。

#### 计算规则

原则：

- 数值越大优先级越高。

- 后来居上原则：当 CSS 选择器的优先级数值一样的时候，后渲染的选择器的优先级更高。

举例：

- `body.foo:not([dir])`，出现了 1 个标签选择器 body，1 个类名选择器`.foo`和 1 个否定伪类`:not`，以及属性选择器`[dir]`，计算结果是 1+10+0+10，也就是 21。

- `html[lang] > body.foo`，出现了 1 个标签选择器 html，1 个属性选择器`[lang]`和 1 个类名选择器`.foo`，计算结果是 1+10+10，也就是 21。

