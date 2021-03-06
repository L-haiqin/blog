### 手写call()

- `函数名称.call(obj,arg1,arg2...argN);`
- 参数说明：
- obj：this要指向的对象,
- arg1,arg2...argN ：参数列表，参数与参数之间使用一个逗号隔开
- 临时改变一次this指向，并立即执行

```javascript
// 手写call()
function person(a,b,c){
	// 返回一个对象
	return {
		name: this.name,
		a:a,
		b:b,
		c:c
	}
}

var obj = {name:'那些年'};

Function.prototype.newCall = function(obj){
	var obj = obj || window; // 不能忘了：如果是空对象，就赋值window
    
	// console.log(this); // function person()，说明此时的this指向的是person()这个函数
	obj.p = this; // 关键点
    
	var newArguments = [];
	// 当实参的个数比形参的个数多的时候，则可以通过arguments来访问实参。
	// 把除了第一个参数this以外的其他参数保存下来
	for(var i = 1; i<arguments.length; i++){
		newArguments.push(arguments[i]);
	}
    
	// console.log(Object.prototype.toString.call(...newArguments)); // [object String]
	var result = obj.p(...newArguments);
	delete obj.p;
	return result;
}

console.log("原生call：",person.call(obj,'参数1','参数2','参数3'))

console.log("手写call：",person.newCall(obj,'参数1','参数2','参数3'))
// Object { name: "", a: "参数1", b: "参数2", c: "参数3" }
// 至此，手写call结束啦~~
```

### 手写apply()

- `函数名称.apply(obj,[arg1,arg2...,argN]);`
- 参数说明：
- obj：this要指向的对象
- [arg1,arg2...argN]：参数列表，要求格式为**数组**
- 临时改变一次this指向，并立即执行

```javascript
// 手写apply()
function person(a,b,c){
	// 返回一个对象
	return {
		name: this.name,
		a:a,
		b:b,
		c:c
	}
}

var obj = {name:'那些年'};

Function.prototype.newApply = function(obj,arr){
	var obj = obj || window; // 不能忘了：如果是空对象，就赋值window
	var result;
	
	// console.log(this); // 打印function person()，说明此时的this指向的是person()这个函数
	obj.p = this;
	
	// 先进行一个判断，判断是否带有参数数组
	if(!arr){
		result = obj.p();
	}else{
		var newArguments = [];
		// 当实参的个数比形参的个数多的时候，则可以通过arguments来访问实参。
		// 把除了第一个参数this以外的其他参数保存下来
		// 注意此时i是从0开始，因为这时只是针对数组arr
		for(var i = 0; i<arr.length; i++){
			newArguments.push(arr[i]);
		}
		
		// console.log(Object.prototype.toString.call(...newArguments)); // [object String]
		result = obj.p(...newArguments);
	}

	delete obj.p;
	return result;
}

console.log("原生call：",person.apply(obj,['参数1','参数2','参数3']))

console.log("手写call：",person.newApply(obj,['参数1','参数2','参数3']))
```

### 手写bind()

- `函数名称.bind(obj,arg1,arg2...argN);`
- 参数说明：
- obj：this要指向的对象,
- arg1,arg2...argN ：参数列表，参数与参数之间使用一个逗号隔开（**可以分多次传入**，call则必须一次性传入所有参数）
- 改变this指向后不会立即执行，**返回一个永久改变this指向的函数**



参考博客：[手写call、apply、bind实现及详解](https://juejin.cn/post/6844903773979033614#heading-5)           