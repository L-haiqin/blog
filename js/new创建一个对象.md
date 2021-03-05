> ### new创建一个对象
>
> ```javascript
>  function Father(name) {
>      this.name = name;
>  }
>  let son = new Father('Lisa');
>  console.log(son); //Father {name: "Lisa"}
> ```
>
> - 创建一个空对象 `son {}`
> - 为 `son` 准备原型链连接`son.__proto__ = Father.prototype`
> - 重新绑定`this`，使构造函数的`this`指向新对象`Father.call(this)`
> - 为新对象属性赋值`son.name`
> - 返回this `return this`，此时的新对象就拥有了构造函数的方法和属性了
>
> 例题：
>
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210201180827968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
>
> - new过程的最后一步，如果函数没有返回值，就自动返回这个新对象，这样就可以拥有构造函数的方法和属性