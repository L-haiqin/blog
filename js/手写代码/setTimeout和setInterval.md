### setTimeout和setInterval

#### setTimeout(fn,0)

如果代码中设定了一个 `setTimeout`，那么浏览器便会在合适的时间，将代码插入任务队列，如果这个时间设为
0，就代表**立即插入队列**，但不是立即执行，仍然要等待前面代码执行完毕。所以`setTimeout` 并不能保证执行的时间，是否及时执行取决于 JavaScript 线程是拥挤还是空闲。

```javascript
var fuc = [1,2,3];
for(var i in fuc){
  setTimeout(function(){console.log(fuc[i])},0);
  console.log(fuc[i]);
}

// 打印：1 2 3 3 3 3
```

- setTimeout在循环结束之后才会执行，每一次循环，把setTimeout放到任务队列里面，所以循环结束之后打印三个3

另一个例子：

```javascript
        var isEnd = true;
        window.setTimeout(function () {
            isEnd = false;//1s后，改变isEnd的值
        }, 1000);
        //这个while永远的占用了js线程，所以setTimeout里面的函数永远不会执行
        while (isEnd);
        //alert也永远不会弹出
        alert('end');
```

[js中setTimeout()时间参数设置为0的探讨](https://www.cnblogs.com/suspiderweb/p/4943473.html)

#### DOM事件

##### 1、点击事件

onclick ：单击事件

ondblclick：双击事件

##### 2、焦点事件

onblur：失去焦点

一般用于表单验证

```html
<body>
<input id="username">
<script>
    /*
    失去焦点
     */
    document.getElementById("username").onblur = function (){
        alert("失去焦点");
    }
</script>
</body>
```

##### 3、加载事件

onload :一张页面或一幅图像完成加载。

##### 4、鼠标事件

onmousedown鼠标按钮被按下。

onmouseup 鼠标按键被松开。

onmousemove 鼠标被移动。

onmouseover 鼠标移到某元素之上。

onmouseout 鼠标从某元素移开。

```javascript
document.getElementById("username").onmousedown = function (event){
    alert(event.button);
}
```

##### 5、键盘事件

onkeydown某个键盘按键被按下

onkeyup某个键盘按键被松开

onkeypress某个键盘按键被按下并松开

执行顺序：**先执行keydown事件，再执行keypress事件，执行keypress事件之后改变dom元素的状态（这里是input的value改变了），再执行keyup**。

而keypress事件发生时，dom元素的状态还未改变，keypress事件之后dom元素的状态才发生改变，所以可以通过setTimeout延迟执行就能达到期望的结果。

```javascript
// 方式一
$('input').onkeypress = function(){
  setTimeout(function(){$('preview').innerHTML = $('input').value;},0);
}
// 方式二
$('input').onkeyup = function(){
  $('preview').innerHTML = $('input').value;
}
```

##### 6、选择和改变

onchange域的内容被改变

onselect文本被选中

##### 7、表单事件

onsubmit确认按钮被点击

onreset重置按钮被点击

#### 用setTimeout实现setInterval

`setTimeout()` ：在指定的毫秒数后调用函数或计算表达式，只执行一次。

`setInterval()` ：按照指定的周期（以毫秒计）来调用函数或计算表达式。方法会不停地调用函数，直到 clearInterval() 被调用或窗口被关闭。

```javascript
// 递归调用
function mySetInterval(func, timeout){
  setTimeout(function fn(){
    func();
    setTimeout(fn, timeout);
  }, timeout)
}
```

