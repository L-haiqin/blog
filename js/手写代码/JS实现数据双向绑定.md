### JS实现数据双向绑定

实现一个数据双向绑定，input值发生变化的同时，`<span>`标签同步input的内容。

```javascript
<div>
    <input type="text" id="input_1">
    <span id="span_1"></span>
</div>
<script type="text/javascript">
    var obj = {};
    Object.defineProperty(obj, 'test', {
        set: (newVal)=>{
            //document.getElementById('input_1').value = newVal;
            document.getElementById('span_1').innerHTML = newVal;
        },
        // 此处不需要get方法
        get: ()=>{
            alert('数据被读取了')
        }
    });
    document.addEventListener('keyup', (e)=>{
        obj.test = e.target.value;      //事件监听，input输入框发生改变，则obj.test属性(相当于一个中间媒介)也会发生改变，进而更改span标签的内容
    })
</script>
```
截图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/74a04895a1e44ce0a4a248e836b5bb2c.png)



参考博客：[参考1](https://blog.csdn.net/weixin_40121676/article/details/100539366)、[参考2](https://www.jianshu.com/p/6e96afee3d61)