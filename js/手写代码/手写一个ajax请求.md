### 手写一个ajax请求

手写一个ajax请求，并在成功后打印success字符串，要求：

（1）使用原生XHL；

（2）请求地址为`https://m.vip.com/`；

（3）使用get方法。

```javascript
<!--html部分，创建一个按钮控件-->
<span id="ajaxButton" style="cursor: pointer; text-decoration: underline">
  Make a request
</span>

<script type="text/javascript">
(function() {
  var xhr;//声明一个变量，用来实例化XMLHttpRequest对象
  document.getElementById("ajaxButton").onclick = function() { myAjax('https://segmentfault.com/a/1190000008097712'); }; //这里将AJAX操作封装在makeRequest函数中，函数的参数为要请求的url，即根目录下的test.html文件。
    
    // 获取xhr对象
    function getXHR(){
      var xhr = null;
      if(window.XMLHttpRequest) {
        xhr = new XMLHttpRequest();
      } else if (window.ActiveXObject) {
        try {
          xhr = new ActiveXObject("Msxml2.XMLHTTP");
        } catch (e) {
          try {
            xhr = new ActiveXObject("Microsoft.XMLHTTP");
          } catch (e) { 
            alert("您的浏览器暂不支持Ajax!");
          }
        }
      }
      return xhr;
    }
  
  function myAjax(url) {
    // 1.创建XMLHttpRequest对象
    xhr = getXHR();
    if (!xhr) {
      alert('Giving up :( Cannot create an XMLHTTP instance');
      return false;
    }
    // 2.指定响应函数为alertContents
    xhr.onreadystatechange = alertContents;
    // 3.打开链接
    xhr.open('GET', url); // 指定请求方法为GET，也可以通过传入参数来指定
    // 4.发送请求
    xhr.send();
  }
  
  // 创建响应函数alertContents
  function alertContents() {
    if (xhr.readyState === XMLHttpRequest.DONE) {
      if (xhr.status === 200) {
        alert(xhr.responseText);
        // alert('success');
      } else {
        alert('There was a problem with the request.');
      }
    }
  }
})();//这是一个立即执行函数
</script>
```



截图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/f101bd06de674deebc2f7f922f0c7110.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAYeWgheW8t-eahOazoeayqw==,size_20,color_FFFFFF,t_70,g_se,x_16)



参考博客：[手写一个ajax请求](https://blog.csdn.net/yesir_mao/article/details/93185843)、[AJAX总结（二），手写AJAX](https://segmentfault.com/a/1190000008097712)