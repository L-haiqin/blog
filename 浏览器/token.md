### token

token是一种身份认证的`令牌`，一般由 `uid+time+sign(签名)+[固定参数]` 组成。

token在客户端一般存放于localStorage，cookie，或sessionStorage中。在服务器一般存于数据库中

token 的流程：

- 用户登录，成功后服务器返回Token给客户端。
- 客户端收到数据后将 token 保存在客户端
- 客户端再次访问服务器，将token 放入**请求头中或者请求参数中**
- 服务器端根据 token 进行校验。校验成功则返回请求数据，校验失败则返回错误码

### token 是怎么加密的

- 通过一个`随机数和加密算法`，生成对应的 token，返回给前端。
- 前端每次请求时带上 token
- 后端用相同的算法解密

### 为什么会劫持cookie，不会劫持token

因为 cookie 登录后，后端会记录 sessionid 并返回，客户端每次请求都带上这个 sessionid，服务端通过 sessionid 去验证身份之类的。所以如果别人拿了你的 cookie 就可以取代你。

而 token 是每次手动添加到 **请求头或者请求参数里**的。浏览器不会自动携带 token，所以不会劫持 token。

### token无感刷新

https://juejin.cn/post/7454474417318690831#heading-5

- 存在两个cookie，一个叫accss token（过期时间较短），一个叫refresh token（过期时间较长）
- 如果accsee token过期的话，在axios response拦截器里面判断状态码，如果为401表示登录过期，这个时候调refreshToken api去刷新token，如果返回的access token有值的话，就重新放置到local storage继续请求；否则就重定向到/login页面，提示用户登录

![image-20250326205045742](/Users/lihaiqin/Library/Application Support/typora-user-images/image-20250326205045742.png)

![image-20250326205057263](/Users/lihaiqin/Library/Application Support/typora-user-images/image-20250326205057263.png)

### 单点登录SSO

一个规模大点的公司大概率会有很多子系统，每个子系统都是属于公司的，没必要为每个子系统做一个登录系统，因为用户是相通的，把每个子系统的登录部分抽离出来形成一个认证中心，这就是单点登录(Single Sign On)。

常见的OAuth2就属于单点登录。

