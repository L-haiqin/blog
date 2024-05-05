### 关于谷歌Chrome浏览器sessionStorage的跨tab页共享问题

面试问题：sessionStorage同一个浏览器同源在不同的tab页里数据共享吗？

> 能否被共享，取决于标签页被打开的方式。

#### MDN解释

MDN中描述到：

- 页面会话在浏览器打开期间一直保持，并且重新加载或恢复页面仍会保持原来的页面会话。
- 在**新标签或窗口打开一个页面时会复制顶级浏览会话的上下文作为新会话的上下文**，这点和 session cookies 的运行方式不同。（共享）
- 打开多个相同的URL的Tabs页面，会创建各自的sessionStorage。（不共享）
- 关闭对应浏览器窗口（Window）/ tab，会清除对应的sessionStorage。

鉴于其中的第二点于是做了几次实验：

- 通过新建标签页复制打开对应URL页面，情况属于第三条，创建独立sessionStorage。（不共享）
- 通过**window.open()**打开新标签页，共享了原tab页中的sessionStorage。（共享）
- 通过**a标签**的_blank方式打开新tab页，Chrome浏览器共享了sessionStorage而Firefox浏览器并没有共享。（Chrome共享）

#### 问题及解决方法

描述：在当前页面下开启一个新的同源tab页sessionStorage中的内容未有效共享到新开tab页中，导致新开tab页sessionStorage为空。

可以通过下面两种方式实现共享：

- 使用`window.open()`方法打开这个同源tab页即可实现sessionStorage的有效共享。
- 通过`window.addEventListener('storage', () => {})`事件监听，来进行同源跨tab页的数据共享。

例子解释：https://blog.csdn.net/sinat_36521655/article/details/82257252