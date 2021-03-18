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
> 4）有两个网址：www.baidu.com（百度）和news.baidu.com（百度新闻），如果现在先打开百度这个网页登录百度账号，再在另一个窗口打开百度新闻网页，那此时需要重新登录百度账号吗？
>
> 提示：父域cookie，认证中心

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