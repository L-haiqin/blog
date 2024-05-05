### js性能优化

#### 1、垃圾回收

（1）当数据不再使用的时候，将其值设置为null来解除引用。

（2）尽量避免使用闭包。

```javascript
function assignHandler(){
  let element = document.getElementById("test");
  element.onclick = function(){
    alert(element.id)    
  }          
}

//以上会导致element的引用数无法被回收，更改如下
function assignHandler(){
  let element = document.getElementById("test");
  let id = element.id;
  
  element.onclick = function(){
    alert(id)
  }          
  element = null;  
}
```

#### 2、事件委托

#### 3、循环

- 能用for缓存的方法循环就用for循坏，性能最高，写起来繁杂；
- 不追求极致性能的情况下，建议使用`forEach`方法（不能break、continue和return）；
- 想尝鲜使用ES6语法的话，不考虑兼容性情况下，推荐使用`for of`方法，这是最简洁、最直接的遍历数组元素的语法；
- 能避免`for in`循环尽量避免，太消费性能、太费时间，数组循环不推荐使用。

#### 4、if-else和switch-case的选择

#### 5、作用域

（1）若某处循环使用**全局变量**时，我们可以略做修改，例子如下

```javascript
//假设有多个img标签的内容，循环中引用了多次document全局变量
function updateUI(){
  let imgs = document.getElementsByTagName("img")
  for (let i = 0; len = imgs.length; i < len; ++i){
    imgs[i].title = document.title + " image “ + i  
  }    

  let msg = document.getElementById("msg");
  msg.innerHTML = "Update";    
}

//改善点
function updateUI(){
  let doc = document
  let imgs = doc.getElementsByTagName("img")
  for (let i = 0; len = imgs.length; i < len; ++i){
    imgs[i].title = doc.title + " image “ + i  
  }    

  let msg = doc.getElementById("msg");
  msg.innerHTML = "Update";    
}
```

（2）尽量少用with，因为with会增加其中执行代码的作用域链的长度

（3）尽可能使用let、const而不是var

#### 6、最小化语句数

（1）例如进行多个声明时，我们可以进行组合，

```javascript
//多个声明
let count = 5；
let color = "blue";
let values = [1, 2, 3];

//组合成一个
let count = 5，
     color = ”blue",
     values = [1, 2, 3]
```

（2）例如插入迭代值时，

```javascript
//修改前
let name = values[i];
i++;

//修改后
let name = values[i++]
```

（3）使用数组和对象字面量时，避免使用构造函数，

```javascript
//修改前
let values = new Array();
values[0] = 123;
values[1] = 456;
values[2] = 789;

let person = new Object();
person.name = "Eric";
person.age = 20;

//修改后
let values = [123, 456, 789]
let person = {
  name: "Eric",
  age:20,    
}
```

（4）减少不必要的变量，删除未使用的js代码

```javascript
test = () => {
	let a = 1, b = 2
	return a + b
}

// 修改后
test = () => {
	return 1 + 2
}
```

#### 7、DOM操作的优化

（1）尽量调用少次数的innerHTML

创建DOM节点最好使用innerHTML方法，因为innerHTML设置值时，后台会创建HTML解析器，然后使用内部的DOM调用来创建DOM结构，而非基于JS的DOM调用。

调用一次innerHTML，就会进行一次现场刷新，循环插入DOM结构时，应注意尽量调用少次数的innerHTML，代码如下

```javascript
//错误方法，做了很多次现场刷新
let list = document.getElementById("myList"),
    i;

for (i = 0; i < 10; ++i){
  list.innerHTML = html+= "<li>Item " + i + "</li>"  
}


//正确方法，尽管在字符串连接上有性能损失，但却只做了一次现场刷新
let list = document.getElementById("myList"),
    html = "",
    i;

for (i = 0; i < 10; ++i){
  html += "<li>Item " + i + "</li>"  
}

list.innerHTML = html
```

（2）减少重排重绘

#### 8、合理使用缓存

参考博客：[Web篇之JS性能优化](https://www.cnblogs.com/tianshu/p/10555921.html)