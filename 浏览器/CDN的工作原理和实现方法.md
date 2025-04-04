### CDN的工作原理和实现方法

#### 为什么会出现CDN？

同志们有没有想过为什么双十一的时候这么多人抢购，流量超级大，为什么很少出现网页加载不出来，也没见过淘宝服务器奔溃呢？

这其中，CDN就发挥着关键的作用。比如，淘宝会把商品等静态资源分发到全国各个地方的网络节点上，我们访问的时候实际是从离我们最近的那台服务器上面下载资源，这就是CDN。CDN依靠着强大的服务器集群能力为网站提供缓存加速服务。

#### 什么是CDN？

CDN（Content Delivery Network）是构建在网络之上的**内容分发网络**，依靠部署在各地的边缘服务器，通过中心平台的负载均衡、内容分发、调度等功能模块，使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。CDN的关键技术主要有**内容存储和分发技术**。

#### CDN的工作原理

CDN之所以快，靠的是**多节点、多线路、用缓存**。

##### 多节点

也就是多服务器，在网络各处放置节点服务器，构成在现有的互联网基础之上的一层智能虚拟网络，CDN系统能够实时地根据网络流量和各节点的连接、负载状况以及到用户的距离和响应时间等综合信息将用户的请求重新导向离用户最近的服务节点上。

##### 多线路

可以有效避免跨线路互联不互通的问题。

##### 用缓存

广泛采用各种缓存服务器，将这些缓存服务器分布到用户访问相对集中的地区或网络中，在用户访问网站时，利用全局负载技术将用户的访问指向距离最近的工作正常的缓存服务器上，由缓存服务器直接响应用户请求。另外，CDN服务器知道源站的资源发生变化后，会通知其它服务器同时更新资源。

#### CDN的实现方法

##### 简单网络请求模型

当我们在浏览器中输出网址时，浏览器首先会将该网址发送到DNS服务器来获取该域名的IP地址，得到IP地址之后，再利用ip地址访问站点服务器，然后站点服务器发送回网页内容。

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ud2MxMjM0LmNuL3dwLWNvbnRlbnQvdXBsb2Fkcy8yMDIwLzAzLzExNGU1ZWUzN2U2ZjJlLmpwZw?x-oss-process=image/format,png#pic_center) 

##### CDN的简单实现

CDN在用户和站点服务器之间加入了一个新的缓存区，用户获取内容不是直接通过站点服务器，而是通过访问CDN节点来获取网页内容。

简单的流程就是，首先用户发送访问请求，智能调度DNS为用户分配适合的CDN节点的IP地址，用户利用该IP地址来获取CDN节点上的内容。CDN节点的内容由站点服务器进行发送，CDN节点再给用户发送内容的同时，也在缓存服务器本地保存了相关内容供下一次用户进行访问。

 ![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ud2MxMjM0LmNuL3dwLWNvbnRlbnQvdXBsb2Fkcy8yMDIwLzAzLzExZTRjMGVmNmVlNWI4LmpwZw?x-oss-process=image/format,png#pic_center) 



我们可以看出，加入CDN之后解决了下面三个问题：

- 从技术上解决了服务器因为带宽小、访问人数多造成访问速度过慢的问题；
- 提高了服务器的安全性；
- 改善了网络的服务质量，提高网站服务的承载能力，提高用户访问网站的相应速度。
  