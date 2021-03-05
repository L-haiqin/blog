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