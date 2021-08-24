### css优先级

优先级是基于不同种类**选择器**组成的匹配规则。

优先级就是分配给指定的 CSS 声明的一个**权重**，它由 匹配的选择器中的 每一种选择器类型的 数值 决定。

当CSS选择器权重相同，则最后的声明的CSS选择器覆盖靠前的 CSS。

**选择器类型的优先级**（以下递增的）：

- 类型选择器（例如，`h1`）和伪元素（例如，`::before`）

- 类选择器(例如，`.example`)，属性选择器（例如，`[type="radio"]`）和伪类（例如，`:hover`）

- ID 选择器例如，`#example`）。

**通配选择符**（universal selector）（[`*`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Universal_selectors)）**关系选择符**（combinators）（[`+`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Adjacent_sibling_combinator), [`>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Child_combinator), [`~`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/General_sibling_combinator), ['` `'](https://developer.mozilla.org/en-US/docs/Web/CSS/Descendant_combinator), [`||`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Column_combinator)）和 **否定伪类**（negation pseudo-class）（[`:not()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:not)）对优先级没有影响。（但是，在  `:not()` 内部声明的选择器会影响优先级）。

优先级为：（外部样式）`External style sheet` **<**（内部样式）`Internal style sheet` **<**（内联样式）`Inline style`