### url中的`#`号

hash 属性是一个可读可写的字符串，该字符串是 URL 的锚部分（从 # 号开始的部分）

#### 1.“#”代表网页中的一个位置。其右面的字符，就是该位置的标识符

比如：​                  

```html
http://www.example.com/index.html#print
```

就代表网页index.html的print位置。浏览器读取这个URL后，会自动将print位置滚动至可视区域。（单页应用）

为网页位置指定标识符，有两个方法。一是使用锚点，比如

```html
<a name="print"></a>
```

二是使用id属性，比如  

```html
<div id="print" >。
```

#### 2.HTTP请求不包括'#'

‘#’是用来指导浏览器动作的，对服务器端完全无用。所以，HTTP请求中不包括#。

比如，访问下面的网址，

```
http://www.example.com/index.html#print
```

浏览器实际发出的请求是这样的：

```html
GET /index.html HTTP/1.1
Host: www.example.com
```

可以看到，只是请求index.html，根本没有"#print"的部分。

#### 3. #后的字符

在第一个#后面出现的任何字符，都会被浏览器解读为位置标识符。这意味着，这些字符都不会被发送到服务器端。

比如，下面URL的原意是指定一个颜色值：​           

```
http://www.example.com/?color=#fff
```

但是，浏览器实际发出的请求是：

```html
GET /?color= HTTP/1.1
Host: www.example.com
```

可以看到，"#fff"被省略了。只有将`#`转码为`%23`，浏览器才会将其作为实义字符处理。也就是说，上面的网址应该被写成：

```
http://example.com/?color=%23fff
```

#### 4.改变#不触发网页重载

单单改变#后的部分，浏览器只会滚动到相应位置，不会重新加载网页。
比如，从​                 

```
http://www.example.com/index.html#location1
```

改成

```
http://www.example.com/index.html#location2
```

浏览器不会重新向服务器请求index.html。

#### 5.改变#会改变浏览器的访问历史

每一次改变#后的部分，都会在浏览器的访问历史中增加一个记录，使用"后退"按钮，就可以回到上一个位置。

这对于**ajax应用程序特别有用，可以用不同的#值，表示不同的访问状态**，然后向用户给出可以访问某个状态的链接。

值得注意的是，上述规则对IE 6和IE 7不成立，它们不会因为#的改变而增加历史记录。

#### 6.window.location.hash读取#值

`window.location.hash`这个属性可读可写。读取时，可以用来判断网页状态是否改变；写入时，则会在不重载网页的前提下，创造一条访问历史记录。

#### 7.onhashchange事件

这是一个HTML 5新增的事件，当#值发生变化时，就会触发这个事件。IE8+、Firefox 3.6+、Chrome 5+、Safari 4.0+支持该事件。

它的使用方法有三种： 

```javascript
// 方法一
window.onhashchange = func;
// 方法二
<body onhashchange="func();">
// 方法三
window.addEventListener("hashchange", func, false);
```

对于不支持onhashchange的浏览器，可以用setInterval监控location.hash的变化。

#### 8.Google抓取#的机制

默认情况下，Google的网络蜘蛛忽视URL的#部分。

但是，Google还规定，如果你希望Ajax生成的内容被浏览引擎读取，那么URL中可以使用"#!"，Google会自动将其后面的内容转成查询字符串_escaped_fragment_的值。

比如，Google发现新版twitter的URL如下：

```
http://twitter.com/#!/username
```

就会自动抓取另一个URL：

```
http://twitter.com/?_escaped_fragment_=/username
```

通过这种机制，Google就可以索引动态的Ajax内容。

参考博客：[URL的井号](https://ruanyifeng.com/blog/2011/03/url_hash.html)