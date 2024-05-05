面试问题：

1、cookie支持跨域吗？怎么从一个页面去另一个不同源的页面带上cookie?

> cookie不支持跨域。
>
> 实现跨域的方法。

2、鉴权怎么做的？前端怎么存储 token？

> 前端是通过响应拦截的状态码和标志，进入路由发送请求，商量返回的状态码判断有没有权限。
>
> token可以放在cookie中，也可以存放在localStorage或sessionStorage中。
>
> JWT是token的一种。
>
> token是把登陆凭证做数字签名加密后得到的字符串。

### 概念

#### **什么是 Cookie**

HTTP Cookie（也叫 Web Cookie或浏览器 Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会**在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上**。通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie 使基于无状态的 HTTP 协议记录稳定的状态信息成为了可能。

Cookie 主要用于以下三个方面：

- 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
- 个性化设置（如用户自定义设置、主题等）
- 浏览器行为跟踪（如跟踪分析用户行为等）

#### **什么是 Session**

Session 代表着服务器和客户端一次会话的过程。Session 对象存储特定用户会话所需的属性及配置信息。这样，当用户在应用程序的 Web 页之间跳转时，存储在 Session 对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。**当客户端关闭会话，或者 Session 超时失效**时会话结束。

### 区别

- 作用范围不同，Cookie 保存在**客户端**（浏览器），Session 保存在**服务端**。

- 存取方式的不同，Cookie 只能保存 ASCII，Session 可以存任意数据类型，一般情况下我们可以在 Session 中保持一些常用变量信息，比如说 UserId 等。session中保存的是**对象**，cookie中保存的是**字符串**。

- 有效期不同，Cookie 可设置为**长时间保持**，比如我们经常使用的默认登录功能，Session 一般**失效时间较短**，**客户端关闭或者 Session 超时**都会失效。

- 隐私策略不同，Cookie 存储在客户端，比较容易遭到不法获取，早期有人将用户的登录名和密码存储在 Cookie 中导致信息被窃取（造成**csrf攻击**）；Session 存储在服务端，安全性相对 Cookie 要好一些。

- 存储大小不同， 单个 Cookie 保存的数据不能超过 4K，Session 可存储数据远高于 Cookie。
- **cookie不能跨域**。

面试问题：cookie支持跨域吗？怎么从一个页面去另一个不同源的页面带上cookie?

### 联系

服务端session + 客户端 sessionId（存储在cookie中）配合使用

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130105357664.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)

- 用户向服务器发送用户名和密码
- 服务器验证通过后，在当前对话(session)里面保存相关数据，比如用户角色，登陆时间等;
- 服务器向用户返回一个`session_id`，写入用户的cookie
- 用户随后的每一次请求，都会通过cookie，将`session_id`传回服务器
- 服务端收到 `session_id`，找到前期保存的数据，由此得知用户的身份

### 分布式 Session（单点登录SSO）

#### 1. session 复制

- 任何一个服务器上的 session 发生改变（增删改），该节点会把这个 session 的所有内容序列化，然后广播给所有其它节点，不管其他服务器需不需要 session ，以此来保证 session 同步

**优点：** 可容错，各个服务器间 session 能够实时响应。 
 **缺点：** 会对网络负荷造成一定压力，如果 session 量大的话可能会造成网络堵塞，拖慢服务器性能。

#### 2. 粘性 session /IP 绑定策略

- **采用 Ngnix 中的 ip_hash 机制，将某个 ip的所有请求都定向到同一台服务器上，即将用户与服务器绑定。** 用户第一次请求时，负载均衡器将用户的请求转发到了 A 服务器上，如果负载均衡器设置了粘性 session 的话，那么用户以后的每次请求都会转发到 A 服务器上，相当于把用户和 A 服务器粘到了一块，这就是粘性 session 机制。

**优点：** 简单，不需要对 session 做任何处理。 
 **缺点：** 缺乏容错性，如果当前访问的服务器发生故障，用户被转移到第二个服务器上时，他的 session 信息都将失效。 
 **适用场景：** 发生故障对客户产生的影响较小；服务器发生故障是低概率事件 。
 **实现方式：** 以 Nginx 为例，在 upstream 模块配置 ip_hash 属性即可实现粘性 session。

#### 3. session 共享（常用）

- 使用分布式缓存方案比如 Memcached 、Redis 来缓存 session，但是要求 Memcached 或 Redis 必须是集群
- 把 session 放到 Redis 中存储，虽然架构上变得复杂，并且需要多访问一次 Redis ，但是这种方案带来的好处也是很大的： 
  - 实现了 session 共享；
  - 可以水平扩展（增加 Redis 服务器）；
  - 服务器重启 session 不丢失（不过也要注意 session 在 Redis 中的刷新/失效机制）；
  - 不仅可以跨服务器 session 共享，甚至可以跨平台（例如网页端和 APP 端）

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/12/29/16f523a04fb8b4b8~tplv-t2oaga2asx-watermark.image)

#### 4. session 持久化

- 将 session 存储到数据库中，保证 session 的持久化

**优点：** 服务器出现问题，session 不会丢失 
 **缺点：** 如果网站的访问量很大，把 session 存储到数据库中，会对数据库造成很大压力，还需要增加额外的开销维护数据库。

### 登录方式

#### 1、单系统登录

常见的登录流程：

![登录流程](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/1/5/16f74f3ed7b34a97~tplv-t2oaga2asx-watermark.image)

服务器向用户浏览器发送了一个名为JESSIONID的Cookie，它的值是Session的id值。

单系统实现登录流程：

- **登录**：将用户信息保存在Session对象中 
  - 如果在Session对象中能查到，说明已经登录
  - 如果在Session对象中查不到，说明没登录（或者已经退出了登录）
- **注销（退出登录）**：从Session中删除用户的信息
- **记住我（关闭掉浏览器后，重新打开浏览器还能保持登录状态）**：配合Cookie来用



#### 2、多系统登录

单点登录就是**在多个系统中，用户只需一次登录，各个系统即可感知该用户已经登录。**

实现博客：[不务正业的前端之SSO（单点登录）实践 ](https://juejin.cn/post/6844903641506119687)、[什么是单点登录(SSO)  ](https://juejin.cn/post/6844903845424971783#heading-0)

##### （1）登录

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/7/20/164b8321d97c203c~tplv-t2oaga2asx-watermark.image)

- 用户访问系统1的受保护资源，系统1发现用户未登录，跳转至sso认证中心，并将自己的地址作为参数
- sso认证中心发现用户未登录，将用户引导至登录页面
- 用户输入用户名密码提交登录申请
- sso认证中心校验用户信息，创建用户与sso认证中心之间的会话，称为全局会话，同时创建授权令牌
- sso认证中心带着令牌跳转会最初的请求地址（系统1）
- 系统1拿到令牌，去sso认证中心校验令牌是否有效
- sso认证中心校验令牌，返回有效，注册系统1
- 系统1使用该令牌创建与用户的会话，称为局部会话，返回受保护资源
- 用户访问系统2的受保护资源
- 系统2发现用户未登录，跳转至sso认证中心，并将自己的地址作为参数
- sso认证中心发现用户已登录，跳转回系统2的地址，并附上令牌
- 系统2拿到令牌，去sso认证中心校验令牌是否有效
- sso认证中心校验令牌，返回有效，注册系统2
- 系统2使用该令牌创建与用户的局部会话，返回受保护资源

用户登录成功之后，会与sso认证中心及各个子系统建立会话，用户与sso认证中心建立的会话称为全局会话，用户与各个子系统建立的会话称为局部会话，局部会话建立之后，用户访问子系统受保护资源将不再通过sso认证中心，全局会话与局部会话有如下约束关系

- 局部会话存在，全局会话一定存在
- 全局会话存在，局部会话不一定存在
- 全局会话销毁，局部会话必须销毁

##### （2）注销

![](D:\blog\images\单点登录.png)

 sso认证中心一直监听全局会话的状态，一旦全局会话销毁，监听器将通知所有注册系统执行注销操作。

- 用户向系统1发起注销请求
- 系统1根据用户与系统1建立的会话id拿到令牌，向sso认证中心发起注销请求
- sso认证中心校验令牌有效，销毁全局会话，同时取出所有用此令牌注册的系统地址
- sso认证中心向所有注册系统发起注销请求
- 各注册系统接收sso认证中心的注销请求，销毁局部会话
- sso认证中心引导用户至登录页面

##### （3）CAS

CAS（Central Authentication Service），即中央认证服务，是 Yale 大学发起的一个开源项目，旨在为 Web 应用系统提供一种可靠的单点登录方法。

类似的：OAuth2

既然不能跨域获取，那 CAS 如何做到共享呢？它通过跳转中间域名的方式来实现登录。

页面访问流程如下图：

![流程图](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/1/5/16f74f3f11a6fbad~tplv-t2oaga2asx-watermark.image)

##### （4）SSO单点登录的实现方式

实现单点登录的关键在于，如何让 `Session Id`（或 Token）在多个域中共享。

1. 父域 Cookie
2. 认证中心
3. LocalStorage 跨域

参考博客：[前端常见登录实现方案 + 单点登录方案](https://juejin.cn/post/6933115003327217671#heading-12)

### 如何保持用户的登陆状态？

#### 1、Cookie + Session 登录

#### 2、Token 登录

#### 3、SSO 单点登录

#### 4、OAuth 第三方登录

参考博客：[前端常见登录实现方案 + 单点登录方案](https://juejin.cn/post/6933115003327217671#heading-12)

### 如果浏览器中禁止了 Cookie，如何保障整个机制的正常运转？

- **url重写**：每次请求中都携带一个 SessionID 的参数，也可以 Post 的方式提交，也可以在请求的地址后面拼接 `xxx?SessionID=123456...`。
- **Token 机制**：当用户第一次登录后，服务器根据提交的用户信息生成一个 Token，响应时将 Token 返回给客户端，以后客户端只需带上这个 Token 前来请求数据即可，无需再次登录验证。
- **JWT机制**

### **只要关闭浏览器 ，session 真的就消失了？**

不对。对 session 来说，除非程序通知服务器删除一个 session，否则服务器会一直保留，程序一般都是在用户做 log off 的时候发个指令去删除 session。

然而浏览器从来不会主动在关闭之前通知服务器它将要关闭，因此服务器根本不会有机会知道浏览器已经关闭，之所以会有这种错觉，是大部分 session 机制都使用会话 cookie 来保存 session id，而关闭浏览器后这个 session id 就消失了，再次连接服务器时也就无法找到原来的 session。如果服务器设置的 cookie 被保存在硬盘上，或者使用某种手段改写浏览器发出的 HTTP 请求头，把原来的 session id 发送给服务器，则再次打开浏览器仍然能够打开原来的 session。

恰恰是**由于关闭浏览器不会导致 session 被删除，迫使服务器为 session 设置了一个失效时间，当距离客户端上一次使用 session 的时间超过这个失效时间时，服务器就认为客户端已经停止了活动，才会把 session 删除以节省存储空间。**

Session 一般**失效时间较短**，**客户端关闭或者 Session 超时**都会失效。





### --------------------------------------

> 鉴权（authentication）：验证用户是否拥有访问系统的权利。
> 参考博客：[前端鉴权知识学习 ](https://juejin.cn/post/6844903708938108935#heading-15)

### 1、cookie与session的区别

- cookie数据存放在**客户端**上，session数据存放在**服务器**上

- cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，**考虑到安全应当使用session。用户验证这种场合一般会用 session**

- session保存在服务器，客户端不知道其中的信息；反之，cookie保存在客户端，服务器能够知道其中的信息

- session会在一定时间内保存在服务器上，当访问增多，会比较占用服务器的性能，考虑到减轻服务器性能方面，应当使用cookie

- session中保存的是**对象**，cookie中保存的是**字符串**

- session**不能区分路径**，同一个用户在访问一个网站期间，所有的session在任何一个地方都可以访问到，而cookie中如果设置了路径参数，那么同一个网站中不同路径下的cookie互相是访问不到的

- 单个cookie保存的数据不能超过4k
### 2、cookie与session的联系
Session：用来跟踪用户的状态，可以保存在集群、数据库、文件中；

Cookie：用来记录用户的一些信息，是浏览器实现的一种数据存储功能。

（1）服务端如何识别特定的客户?

这个时候Cookie就登场了。

每次HTTP请求的时候，客户端都会发送相应的Cookie信息到服务端。

实际上大多数的应用都是**用 Cookie来实现Session跟踪**的，第一次创建Session的时候，服务端会在HTTP协议中告诉客户端，需要在 Cookie 里面记录一个**Session ID**，以后每次请求把这个会话ID发送到服务器，我就知道你是谁了。

**session 的运行依赖 session id，而 session id 是存在 cookie中的。**

（2）如果客户端的浏览器禁用了 Cookie怎么办？

一般这种情况下，会使用一种叫做**URL重写**的技术来进行会话跟踪，(在 url 中传递 `session_id`)即每次HTTP交互，URL后面都会被附加上一个诸如 `sid=xxxxx` 这样的参数，服务端据此来识别用户。


（3）Cookie有个很常用的场景就是**自动填上用户名和密码**

（4）会话管理

 像在线购物网站，需要登录的网站等，记住那些人登录过系统，哪些人往自己的购物车中放商品，这就需要进行会话管理。


> 本来 session 是一个抽象概念，开发者为了实现中断和继续等操作，将 user agent 和 server之间一对一的交互，抽象为“会话”，进而衍生出“会话状态”，也就是 session 的概念。 
> 而 cookie 是一个实际存在的东西，http协议中定义在 header 中的字段。可以认为是 session 的一种后端无状态实现。 
> 而我们今天常说的 “session”，是为了绕开cookie 的各种限制，通常借助 cookie 本身和后端存储实现的，一种更高级的会话状态实现。

### 3、如何设置cookie
**客户端设置：**

```javascript
document.cookie = "name=xiaoming; age=12 "
```

- 客户端可以设置cookie的一下选项：`expires, domain, path, secure(`只有在`https`协议的网页中, 客户端设置`secure`类型cookie才能生效)，但无法设置`httpOnly`选项

- 以名/值对形式存储，数据类型为string

- 设置cookie => cookie被自动添加到request header中 => 服务端接收到cookie

**服务端设置：**
- 不管你是请求一个资源文件(如html/js/css/图片)，还是发送一个ajax请求，服务端都会返回response，而response header中有一项叫`set-cookie`, 是服务端专门用来设置cookie的

- 一个`set-cookie`只能设置一个cookie, 当你想设置多个, 需要添加同样多的`set-cookie`

- 服务端可以设置cookie的所有选项：`expires, domain, path, secure, HttpOnly`

### 4、session的使用
服务端session + 客户端 sessionId（存储在cookie中）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130105357664.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- 用户向服务器发送用户名和密码
- 服务器验证通过后，在当前对话(session)里面保存相关数据，比如用户角色，登陆时间等;
- 服务器向用户返回一个`session_id`，写入用户的cookie
- 用户随后的每一次请求，都会通过cookie，将`session_id`传回服务器
- 服务端收到 `session_id`，找到前期保存的数据，由此得知用户的身份

> 如何实现单点登录？

### 5、token的使用
**实现过程：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130110159699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- 用户通过用户名和密码发送请求
- 程序验证
- 程序返回一个签名的token给客户端
- 客户端储存token, 并且每次用于发送请求
- 服务端验证Token并返回数据


**JWT(JSON Web Token)：**

JSON Web Tokens由dot（.）分隔的三个部分组成：

- Header（头部）
- Payload（负载）
- Signature（签名）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130110554264.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)


JWT通常写成如下形式：

```javascript
xxxxx.yyyyy.zzzz
```
**Header（头部）**

Header 是一个 JSON 对象

```javascript
{
  "alg": "HS256", // 表示签名的算法，默认是 HMAC SHA256（写成 HS256）
  "typ": "JWT"  // 表示Token的类型，JWT 令牌统一写为JWT
}
```

**Payload（负载）**

Payload 部分也是一个 JSON 对象，用来存放实际需要传递的数据

```javascript
{
  // 7个官方字段
  "iss": "a.com", // issuer：签发人
  "exp": "1d", // expiration time： 过期时间
  "sub": "test", // subject: 主题
  "aud": "xxx", // audience： 受众
  "nbf": "xxx", // Not Before：生效时间
  "iat": "xxx", // Issued At： 签发时间
  "jti": "1111", // JWT ID：编号
  // 可以定义私有字段
  "name": "John Doe",
  "admin": true
}
```

JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。

**Signature（签名）**

Signature 是对前两部分的签名，防止数据被篡改。

首先，需要指定一个密钥(secret)。这个密钥只有服务器才知道，不能泄露给用户。然后，使用Header里面指定的签名算法（默认是 HMAC SHA256），按照下面的公式产生签名。

```javascript
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```

算出签名后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用"点"（.）分隔，就可以返回给用户。

```javascript
JWT = Base64(Header) + "." + Base64(Payload) + "." + $Signature
```


**如何保证安全？**

- 发送JWT要使用HTTPS；不使用HTTPS发送的时候，JWT里不要写入秘密数据JWT的payload中要设置expire时间

**使用方式：**

- 客户端收到服务器返回的 JWT，可以储存在 `Cookie` 里面，也可以储存在 `localStorage`。此后，客户端每次与服务端通信，都要带上这个JWT。你可以把它放在Cookie里面自动发送，但是这样不能跨域，所以更好的做法是放在HTTP请求的头信息 `Authorization` 字段里面。

```javascript
Authorization: Bearer <token>
```

- 另一种做法是， 跨域的时候， JWT就放在POST请求的数据体里。


参考博客：[详解 Cookie，Session，Token ](https://juejin.cn/post/6844903864810864647)

### 6、WebStorage客户端存储数据
WebStorage提供两种类型的API：`localStorage`和`sessionStorage`
- `localStorage` ：始终有效，窗口或浏览器关闭也一直保存；所有同源窗口都是共享的
- `sessionStorage` ：针对一个 session 的数据存储, 当用户关闭浏览器窗口后，数据会被删除；不在不同的浏览器窗口共享，即使是同一个页面
- `cookie`：只在设置的cookie过期时间（以 UTC 或 GMT 时间）之前一直有效，即使窗口和浏览器关闭；所有同源窗口都是共享的

异同：
- 上述三者都是保存在**浏览器端**
- `cookie`数据始终在同源的http请求中携带（即使不需要），即cookie**在浏览器和服务器间来回传递**
- `sessionStorage`和`localStorage`不会自动把数据发送给服务器，**仅在本地保存**

常用API:

- 保存数据：`localStorage.setItem(key,value);`
- 读取数据：`localStorage.getItem(key);`
- 删除单个数据：`localStorage.removeItem(key);`
- 删除所有数据：`localStorage.clear();`
- 得到某个索引的key：`localStorage.key(index);`