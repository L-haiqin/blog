在 HTML 中会遇到以下三类 script：

- `<script src='xxx'></script>`
- `<script src='xxx' async></script>`
- `<script src='xxx' defer></script>`

那么这三类 script 有什么区别呢？

## script

浏览器在解析 HTML 的时候，如果遇到一个没有任何属性的 script 标签，就会暂停解析，先发送网络请求获取该 JS 脚本的代码内容，然后让 JS 引擎执行该代码，当代码执行完毕后恢复解析。整个过程如下图所示：

![script](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/caf2f618530046658ab8e3b4a8699589~tplv-k3u1fbpfcp-zoom-1.image)

可以看到，script 阻塞了浏览器对 HTML 的解析，**如果获取 JS 脚本的网络请求迟迟得不到响应，或者 JS 脚本执行时间过长，都会导致白屏**，用户看不到页面内容。

## async script（立即下载，停止HTML解析，执行脚本）

async 表示**异步**，例如七牛的源码中就有大量的 async 出现：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2bad108f420844fab2e66e7ee80a217e~tplv-k3u1fbpfcp-zoom-1.image)

 

当浏览器遇到带有 async 属性的 script 时，请求该脚本的网络请求是**异步**的，不会阻塞浏览器解析 HTML，一旦网络请求回来之后，如果此时 HTML 还没有解析完，浏览器会暂停解析，先让 JS 引擎**执行**代码，执行完毕后再进行解析，图示如下：



![script](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/021b5dbeddb64db0a7099dc0a4dd076d~tplv-k3u1fbpfcp-zoom-1.image)



当然，如果在 JS 脚本请求回来之前，HTML 已经解析完毕了，那就啥事没有，立即执行 JS 代码，如下图所示：



![defer2](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4e5a89a4a1fe49ed9d5acaf25ef9aadd~tplv-k3u1fbpfcp-zoom-1.image)



所以 async 是不可控的，**因为执行时间不确定，你如果在异步 JS 脚本中获取某个 DOM 元素，有可能获取到也有可能获取不到。**而且如果存在多个 async 的时候，它们之间的执行顺序也不确定，完全依赖于网络传输结果，谁先到执行谁。

所以，async比较适合一些**第三方脚本**。

## defer script（立即下载，但延迟执行）

defer 表示**延迟**，例如掘金的源码中就有大量的 defer 出现：

![掘金](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6355d82fadb3496fa9ca78499f598e0e~tplv-k3u1fbpfcp-zoom-1.image)

当浏览器遇到带有 defer 属性的 script 时，获取该脚本的网络请求也是异步的，不会阻塞浏览器解析 HTML，一旦网络请求回来之后，如果此时 HTML 还没有解析完，浏览器不会暂停解析并执行 JS 代码，而是**等待 HTML 解析完毕再执行 JS 代码**，图示如下：

![script](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b8313e4787f04c79838fec9961bda0fb~tplv-k3u1fbpfcp-zoom-1.image)

如果存在多个 defer script 标签，浏览器（IE9及以下除外）会保证它们按照在 HTML 中出现的顺序执行，不会破坏 JS 脚本之间的依赖关系。

所以，defer比较适合与dom有关联的脚本。

在解析完之后再执行js代码，能够保证如果该js代码中有操作dom元素的代码正常执行。不存在dom元素找不到的情况。

## 总结

最后，根据上面的分析，不同类型 script 的执行顺序及其是否阻塞解析 HTML 总结如下：

| script 标签      | JS 执行顺序      | 是否阻塞解析 HTML      |
| ---------------- | ---------------- | ---------------------- |
| `<script>`       | 在 HTML 中的顺序 | 阻塞                   |
| `<script async>` | 网络请求返回顺序 | 可能阻塞，也可能不阻塞 |
| `<script defer>` | 在 HTML 中的顺序 | 不阻塞                 |

不管是async还是defer，两者都只适用于**外部脚本**，还要注意兼容性的问题，如果浏览器无法识别这两个属性，还是把script内容放在页面底部比较好。

当script同时有async和defer属性时，执行效果和async一致。

链接：[掘金](https://juejin.cn/post/6894629999215640583)、[红宝书说法](https://blog.csdn.net/qq_43393963/article/details/101389319)

## 补充：css加载会阻塞dom树的解析和渲染吗

https://zhuanlan.zhihu.com/p/43282197

结论：

1. **css加载不会阻塞DOM树的解析**
2. **css加载会阻塞DOM树的渲染。**因为如果边加载边渲染，那就要不断的更改盒子们的坐标和尺寸，就要不断的重绘界面，没有实际意义
3. **css加载会阻塞后面js语句的执行。**因为JS可以动态修改css，那么最终渲染结果就是不可预估的了

因此，为了避免让用户看到长时间的白屏时间，我们应该尽可能的提高css加载速度，比如可以使用以下几种方法:

1. 使用CDN(因为CDN会根据你的网络状况，替你挑选最近的一个具有缓存内容的节点为你提供资源，因此可以减少加载时间)
2. 对css进行压缩(可以用很多打包工具，比如webpack,gulp等，也可以通过开启gzip压缩)
3. 合理的使用缓存(设置cache-control,expires,以及E-tag都是不错的，不过要注意一个问题，就是文件更新后，你要避免缓存而带来的影响。其中一个解决防范是在文件名字后面加一个版本号) ===》就是说保证文件名不一样，可以使用md5
4. 减少http请求数，将多个css文件合并，或者是干脆直接写成内联样式(内联样式的一个缺点就是不能缓存)