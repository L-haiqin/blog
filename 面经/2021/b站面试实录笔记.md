### b站面试实录笔记

1、一个页面有20个按钮，如何统计每一个按钮被点击了多少次

> 使用闭包。
>
> ```javascript
> var num = [];
> for(var i = 0; i < op.length; i++){
>     function(i){
>         op[i].onclick = function(){
>             num[i]++;
>         }
>     }
> }
> 
> 
> ```
>
> ```html
> <!DOCTYPE html>
> <html>
> 	<head>
> 		<meta charset="utf-8">
> 		<title></title>
> 		<style>
> 			*{
> 				margin: 0;
> 				padding: 0;
> 			}
> 			button{
> 				width: 100px;
> 				height: 50px;
> 				background-color: #4CAF50;
> 				padding: 0px 12px;
> 				font-size: 14px;
> 				color: white;
> 				border: none;
> 			}
> 		</style>
> 	</head>
> 	<body>
> 		<div class="container">
> 			<button type="button">按钮1</button>
> 			<button type="button">按钮2</button>
> 			<button type="button">按钮3</button>
> 			<button type="button">按钮4</button>
> 			<button type="button">按钮5</button>
> 		</div>
> 		<script>
> 			var butt = document.getElementsByTagName("button");
> 			
> 			// 使用闭包，为每一个按钮开辟一块内存存储变量num
> 			function getNum(){
> 				var num = 0;
> 				return(function(){
> 					num++;
> 					alert("您点击了我"+num+"次");
> 				})
> 			}
> 			
> 			// 为每一个按钮添加点击事件
> 			for(var i = 0; i<butt.length; i++){
> 				butt[i].onclick = getNum();
> 			}
> 		</script>
> 	</body>
> </html>
> ```


2、v-model上下绑定的底层原理

> defineProperty()，重点了解基本概念

3、promise：几种状态···，asycn，await （重点，概念要知道！！！）

> 经典题目：p1、p2、p3是已经写好的promise请求，直接执行.then就可以发出请求了

4、编写css，实现两列布局，左侧200px固定，右侧宽度自适应

> 基本思想：容器设为flex布局，justify-content: space-between; ，左右固定高度，左侧固定宽度，右侧flex:1;
>
>   ```html
> <!DOCTYPE html>
> <html>
> 	<head>
> 		<meta charset="utf-8">
> 		<title></title>
> 		<style>
> 			*{
> 				margin: 0;
> 				padding: 0;
> 			}
> 			.container{
> 				height: 100px;
> 				display: flex;
> 				justify-content: space-between;
> 			}
> 			.left{
> 				width: 200px;
> 				background-color: #0000FF;
> 			}
> 			.right{
> 				flex: 1;
> 				background-color: #0099FF;
> 			}
> 		</style>
> 	</head>
> 	<body>
> 		<div class="container">
> 			<div class="left"></div>
> 			<div class="right"></div>
> 		</div>
> 	</body>
> </html>
>   ```

5、编写getColor()函数，该函数用于获取颜色，一共有红黄蓝三种颜色，第一次点击按钮获取是红，第二次是黄，第三次是蓝，第四次又是红，但是如果获取颜色的时间间隔超过30s，则重置颜色（也就是说如果第二次点击按钮获取颜色后，等了超过30s才获取第三次，那么就又是红色）。（重点，实际应用场景会用到！！！）

>```html
><!DOCTYPE html>
><html>
>	<head>
>		<meta charset="utf-8">
>		<title></title>
>		<style>
>			*{
>				margin: 0;
>				padding: 0;
>			}
>			button{
>				width: 100px;
>				height: 50px;
>				background-color: #4CAF50;
>				padding: 0px 12px;
>				font-size: 14px;
>				color: white;
>				border: none;
>			}
>		</style>
>	</head>
>	<body>
>		<div class="container">
>			<button type="button">按钮1</button>
>			<button type="button">按钮2</button>
>			<button type="button">按钮3</button>
>			<button type="button">按钮4</button>
>			<button type="button">按钮5</button>
>		</div>
>		<script>
>			let btns=document.getElementsByTagName("button");
>			
>			function getColor(){
>				let status=0;
>				let id=null;
>				const colorArr=["红","黄","蓝"];
>				return()=>{
>					console.log(colorArr[status]);
>					clearTimeout(id);
>                    // 三种颜色循环显示
>					status==2?status=0:status+=1;
>					// 间隔超过30s,重置颜色
>					id=setTimeout(()=>{status=0},30000);
>				}
>			}
>			btns[0].onclick = getColor();
>		</script>
>	</body>
></html>
>```

6、跨域

> 1）是浏览器的行为，不是服务端的行为。
>
> 2）为什么浏览器要有跨域这个行为：同源策略（三要素：协议、域名、端口号）。
>
> 3）为什么要有同源策略：安全？postman有跨域吗
>
> Postman 是一个独立的桌面应用程序，不受同源策略的限制，可以发送跨域请求。但在浏览器中，跨域请求仍然受到同源策略的限制。对于网页版的postman web，会受到同源策略的限制，这意味着它无法直接发送跨域请求，但我们可以考虑使用代理服务器（Whistle，Charles）、跨域资源共享（CORS）来解决。
>
> 4）有两个网址：www.baidu.com（百度）和news.baidu.com（百度新闻），如果现在先打开百度这个网页登录百度账号，再在另一个窗口打开百度新闻网页，那此时需要重新登录百度账号吗？
>
> 通常情况不需要，但是要考虑具体网站的安全策略。
>
> 百度的登录机制通常会使用会话（session）来跟踪用户的登录状态。当您在一个窗口中登录后，在同一浏览器会话中的其他窗口或标签页通常会共享该会话信息。这意味着在您打开百度新闻网页时，会话信息会被共享，因此您通常会保持登录状态，无需重新登录。
>
> 然而，这种行为可能会受到具体网站的实现方式和安全策略的影响。有些网站可能会选择在不同的子域名之间隔离会话信息，这样您在百度上登录后，在百度新闻网页上可能仍然需要重新登录。但是对于百度来说，一般情况下，您在一个子域名上登录后，在其他百度子域名上会保持登录状态。
>
> 需要注意的是，如果您清除了浏览器的缓存或删除了与百度相关的 cookie 信息，可能会导致会话失效，需要重新登录。
>
> 提示：父域cookie，认证中心
>
> - 父域 Cookie：父域 Cookie 是存储在顶级域名（例如，baidu.com）下的 Cookie。在百度的单点登录机制中，当您在一个百度域名下登录时（例如，www.baidu.com），会生成一个父域 Cookie。这个父域 Cookie 包含了与您的登录会话相关的信息，如身份验证凭据和会话标识。由于父域 Cookie 存储在顶级域名下，因此可以在不同的子域名之间共享和识别。
> - 认证中心：认证中心是一个负责管理用户身份验证和授权的中心服务。在百度的单点登录机制中，认证中心起到关键作用。当您在一个百度域名下登录并验证成功后，认证中心会生成一个访问令牌（access token）。该访问令牌是一个安全的令牌，用于表示您已通过身份验证并具有访问权限。认证中心会将该访问令牌与您的账号关联，并在您访问其他使用相同单点登录机制的百度子域名时进行验证。
> - 具体来说，当您在一个窗口中登录百度（例如，www.baidu.com）时，认证中心会验证您的凭据，并为您生成一个访问令牌。同时，会生成一个父域 Cookie，并将该访问令牌与父域 Cookie 相关联。当您在另一个窗口中打开百度新闻网页（例如，news.baidu.com）时，认证中心会检测到您的父域 Cookie，并使用其中的访问令牌进行身份验证。这使得您无需重新登录百度账号，而能够在不同的百度子域名之间保持登录状态。

7、设计模式：工厂/观察者模式（重点，常考，要自己去写）（基础，虽然工作上可能用不到）

8、单页应用（vue），有什么好处（引申：单页应用和多页应用的优劣对比）（基础）

> 单页应用的SEO是比较麻烦的，为什么呢？

9、了解服务端渲染吗：SSR/CSR基本概念和优劣对比（基础）

> 服务端渲染SSR，客户端渲染CSR

10、http缓存，http状态码

> 有一个平台，里面有很多web应用，要求状态码必须返回200才可以进行web交互，如果返回其他状态码就不能进行数据交互了那应该怎么解决呢？
>
> 解决方法：将状态码迁移到response里面。（没理解？？？）

11、浏览器缓存策略：localStorage和sessionStorage

12、前端路由

13、负载均衡