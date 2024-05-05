> 参考博客：[经典面试题：从 URL 输入到页面展现到底发生什么？](https://www.zhihu.com/people/jian-qiang-de-pao-mo-90)、[TCP/IP协议详解](https://blog.csdn.net/weixin_44198965/article/details/90083126)

- DNS 解析：将域名解析成 IP 地址
- TCP 连接：TCP 三次握手 
- 发送 HTTP 请求
- 服务器处理请求并返回 HTTP 报文
- 浏览器解析渲染页面
- 断开连接：TCP 四次挥手

### 1、DNS 解析：将域名解析成 IP 地址

URL（Uniform Resource Locator），统一资源定位符

> scheme://host.domain:port/path/filename 

> 各部分解释如下：
> scheme：定义**因特网服务的类型**。常见的协议有 http、https、ftp、file，其中最常见的类型是 http，而 https则是进行加密的网络传输。
> host：定义**域主机**（http 的默认主机是 www）
> domain：定义因特网**域名**
> port：定义主机上的**端口号**（http 的默认端口号是 80）
> path：定义服务器上的路径（如果省略，则文档必须位于网站的根目录中）。
> filename：定义文档/资源的名称

DNS记录、DNS协议、DNS服务器

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210127130912439.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- 浏览器通过向 DNS 服务器发送域名，DNS 服务器查询到与域名相对应的 IP 地址，然后返回给浏览器，浏览器再将 IP 地址打在协议上，同时请求参数也会在协议搭载，然后一并发送给对应的服务器。

### 2、TCP 连接：TCP 三次握手 （全双工通信）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210127131136553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- 第一次握手：Client将标志位SYN置为1，随机产生一个值seq=J，并将该数据包发送给Server，Client进入SYN_SENT状态，等待Server确认。

- 第二次握手：Server收到数据包后由标志位SYN=1知道Client请求建立连接，Server将标志位SYN和ACK都置为1，ack=J+1，随机产生一个值seq=K，并将该数据包发送给Client以确认连接请求，Server进入SYN_RCVD状态。

- 第三次握手：Client收到确认后，检查ack是否为J+1，ACK是否为1，如果正确则将标志位ACK置为1，ack=K+1，并将该数据包发送给Server，Server检查ack是否为K+1，ACK是否为1，如果正确则连接建立成功，Client和Server进入ESTABLISHED状态，完成三次握手，随后Client与Server之间可以开始传输数据。



**【补充：tcp报文格式】**



![img](https://images2015.cnblogs.com/blog/964016/201608/964016-20160829215953168-1927861560.png)



- 32位序号seq (sequence number)

- 32位确认序号ack（acknowledgement number）
- URG：紧急标志。紧急标志为"1"表明该位有效。
- ACK：确认标志。表明确认编号栏有效。
- PSH：推标志。
- RST：复位标志。
- SYN：同步标志。表明同步序列编号栏有效。该标志仅在三次握手建立TCP连接时有效。
- FIN：结束标志。

### 3、发送 HTTP 请求

请求报文由请求行（request line）、请求头（header）、请求体三个部分组成。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021012713194675.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- 请求方法包含 8 种：GET、POST、PUT、DELETE、PATCH、HEAD、OPTIONS、TRACE。

> POST /chapter17/user.html HTTP/1.1

> 以上代码中，“POST”代表请求方法，“/chapter17/user.html”表示 URL，“HTTP/1.1”代表协议和协议的版本。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210127132349504.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

### 4、服务器处理请求并返回 HTTP 报文

响应报文由响应行（request line）、响应头部（header）、响应主体三个部分组成。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021012713331994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

**状态码规则如下：**

- 1xx：指示信息--表示请求已接收，继续处理。
- 2xx：成功--表示请求已被成功接收、理解、接受。
- 3xx：重定向--为完成请求，客户需进一步细化请求。
- 4xx：客户端错误--请求有语法错误或请求无法实现。
- 5xx：服务器端错误--服务器未能实现合法的请求。

**具体如下：**
- 200 ok（请求成功）
- 204 no content （请求成功，但是没有结果返回）
- 206 partial content （客户端请求一部分资源，服务端成功响应，返回一范围资源）
- 301 move permanently （永久性重定向）
- 302 found （临时性重定向）
- 303 see other （示由于请求对应的资源存在着另一个 URI，应使用 GET方法定向获取请求的资源）
- 304 not modified （表示在客户端采用带条件的访问某资源时，服务端找到了资源，但是这个请求的条件不符合。跟重定向无关）
- 307 temporary redirect （跟302一个意思）
- 400 bad request （请求报文存在语法错误）
- 401 unauthorized （需要认证（第一次返回）或者认证失败（第二次返回））
- 403 forbidden （请求被服务器拒绝了）
- 404 not found （服务器上无法找到请求的资源）
- 500 internal server error （服务端执行请求时发生了错误）
- 503 service unavailable （服务器正在超负载或者停机维护，无法处理请求）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210127133418448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

**服务器：**

- 服务器是网络环境中的**高性能计算机**，它侦听网络上的其他计算机（客户机）提交的服务请求，并提供相应的服务，比如**网页服务、文件下载服务、邮件服务、视频服务**。

- 而客户端主要的功能是浏览网页、看视频、听音乐等等，两者截然不同。

- 每台服务器上都会安装处理请求的应用——web server。常见的 web server 产品有 **apache、nginx、IIS 或 Lighttpd** 等。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210127132612229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

**实现过程：**

- 首先浏览器发送过来的请求先经过控制器，控制器进行逻辑处理和请求分发；

- 接着会调用模型，这一阶段模型会获取 redis db 以及 MySQL 的数据；

- 获取数据后将渲染好的页面，响应信息会以响应报文的形式返回给客户端；

- 最后浏览器通过渲染引擎将网页呈现在用户面前。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210127132658392.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

### 5、浏览器解析渲染页面

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210127134506300.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- **根据 HTML 解析出 DOM 树**
  - 深度优先遍历
  - 构建 DOM 树的过程中，若遇到 script 标签，则 DOM 树的构建会暂停，直至脚本执行完毕。

- **根据 CSS 解析生成 CSS 规则树**
  - 解析 CSS 规则树时 js 执行将暂停，直至 CSS 规则树就绪。

- **结合 DOM 树和 CSS 规则树，生成渲染树**
  - 精简 CSS 并可以加快 CSS 规则树的构建，从而加快页面相应速度。

- **根据渲染树计算每一个节点的信息**
  - 布局：通过渲染树中渲染对象的信息，计算出每一个渲染对象的位置和尺寸。
  - 回流：在布局完成后，发现了某个部分发生了变化影响了布局，那就需要倒回去重新渲染。

- **根据计算好的信息绘制页面**
  - 重绘：某个元素的背景颜色，文字颜色等，不影响元素周围或内部布局的属性，将只会引起浏览器的重绘。
  - 回流：某个元素的尺寸发生了变化，则需重新计算渲染树，重新渲染。

### 6、断开连接：TCP 四次挥手

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210127135208843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

- 第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态。

- 第二次挥手：Server收到FIN后，发送一个ACK给Client，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号），Server进入CLOSE_WAIT状态。

- 第三次挥手：Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入LAST_ACK状态。

- 第四次挥手：Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给Server，确认序号为收到序号+1，Server进入CLOSED状态，完成四次挥手。


> 为什么建立连接是三次握手，而关闭连接却是四次挥手呢？
>
> 因为服务端在LISTEN状态下，收到建立连接请求的SYN报文后，把ACK和SYN放在一个报文里发送给客户端。而关闭连接时，当收到对方的FIN报文时，仅仅表示对方不再发送数据了但是还能接收数据，己方也未必全部数据都发送给对方了，所以己方可以立即close，也可以发送一些数据给对方后，再发送FIN报文给对方来表示同意现在关闭连接，因此，己方ACK和FIN一般都会分开发送。