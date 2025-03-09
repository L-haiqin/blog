```js
// 1. call
Function.prototype.myCall = function(context, ...args) {
    // ​**处理 context 为 null 或 undefined 的情况，默认指向全局对象（浏览器中为 window）​**
    context = context || globalThis; 
    
    // ​**避免覆盖 context 原有属性，用 Symbol 生成唯一键**
    const fnKey = Symbol('fnKey'); 
    context[fnKey] = this; // this 是原函数,函数作为对象的方法调用时，this会指向该对象
    
    // ​**执行函数并保存结果**
    const result = context[fnKey](...args); 
    
    // ​**清理临时属性**
    delete context[fnKey]; 
    
    return result;
};

// 测试用例
const obj = { value: 100 };
function test(a, b) { return this.value + a + b; }
console.log(test.myCall(obj, 1, 2)); // 输出 103


// 2.apply
Function.prototype.myApply = function(context, argsArray) {
    // ​**处理参数数组未传递或非数组的情况**
    argsArray = Array.isArray(argsArray) ? argsArray : [];
    
    context = context || globalThis;
    const fnKey = Symbol('fnKey');
    context[fnKey] = this;
    
    // ​**使用展开语法传递数组参数**
    const result = context[fnKey](...argsArray);
    delete context[fnKey];
    
    return result;
};

// 测试用例
console.log(test.myApply(obj, [3, 4])); // 输出 107


// 3.bind
Function.prototype.myBind = function(context, ...bindArgs) {
    const originalFunc = this; // 原函数
    
    // ​**返回的新函数可以接收新参数**
    const boundFunc = function(...callArgs) {
        // ​**判断是否通过 new 调用（构造函数场景）​**
        const isNewCall = this instanceof boundFunc;
        
        // ​**如果是构造函数调用，this 指向新对象，否则使用绑定的 context**
        const finalContext = isNewCall ? this : context;
        
        // ​**合并绑定参数和调用参数**
        const finalArgs = bindArgs.concat(callArgs);
        
        // ​**执行原函数**
        return originalFunc.apply(finalContext, finalArgs);
    };
    
    // ​**维护原型关系：新函数的原型指向原函数的原型**
    if (originalFunc.prototype) {
        // ​**使用空函数中转避免直接修改 boundFunc.prototype**
        const EmptyFunc = function() {};
        EmptyFunc.prototype = originalFunc.prototype;
        boundFunc.prototype = new EmptyFunc();
    }
    
    return boundFunc;
};

// 测试用例
const boundTest = test.myBind(obj, 5);
console.log(boundTest(6)); // 输出 111

// 构造函数场景测试
function Person(name) { this.name = name; }
const BoundPerson = Person.myBind(null);
const person = new BoundPerson('John');
console.log(person.name); // 输出 "John"
```

有一个关键点：**函数作为对象的方法调用时，this会指向该对象**。所以会用对象的一个key来保存原函数，像这样子context[fnKey] = this;

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
// Object { name: "那些年", a: "参数1", b: "参数2", c: "参数3" }
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

```javascript
let Person = {
    name: 'Tom',
    say() {
        console.log(this)
        console.log(`我叫${this.name}`)
    }
}

Person1 = {
    name: 'Tom1'
}

// 重写bind
Function.prototype.myBind = function(obj) {
    //返回一个绑定this的函数，我们需要在此保存this
    let self = this
    // 可以支持柯里化传参，保存参数
    let arg = [...arguments].slice(1) // 从下标1开始截取到数组末尾
    // 返回一个函数
    return function() {
        //因为支持柯里化传参，所以需要再次获取参数
        let newArg = [...arguments]
        console.log(newArg)
        // 返回函数绑定this，传入两次保存的参数
        //考虑返回函数有返回值做了return
        return self.apply(obj, arg.concat(newArg))
    }
}

// 测试
let fn = Person.say.myBind(Person1)
fn()
fn(18)
```

关键点：

- 函数调用，改变this
- 返回一个绑定this的函数
- 接收多个参数
- 支持柯里化形式传参 fn(1)(2)

参考博客：[手写call、apply、bind实现及详解](https://juejin.cn/post/6844903773979033614#heading-5)           