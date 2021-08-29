## 响应式原理（数据双向绑定原理）

[彻底搞懂Vue针对数组和双向绑定(MVVM)的处理方式](https://www.cnblogs.com/wangweianger/p/10318255.html)

> 参考博客：[Vue响应式原理（易懂）](https://juejin.cn/post/6844903858850758670#heading-1)、[简单实现vue的响应式](https://github.com/wind-jyf/MVVM)


通过数据劫持结合发布者-订阅者模式的方式来实现，利用`Object.defineProperty()` 重新定义对象获取属性值(get)和设置属性值(set)。 

示意图1：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306165235424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
示意图2：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306165249563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- compile模板编译
- observer数据劫持：管理`Object.defineProperty()`
- dep依赖收集：两个方法
  - 数据被改变，通知依赖去更新，`notify()`
  - 数据被读取，将这个依赖收集起来（通过触发get方法），`depend()`
- watcher：两个方法
  - 通知变化，`update()`
  - 被收集起来到dep中，`addDep()`

示意图3：
![在这里插入图片描述](https://img-blog.csdnimg.cn/202103071250217.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk3MzQxNQ==,size_16,color_FFFFFF,t_70)
- 一个属性可能有多个依赖，每个响应式数据都有一个Dep来管理它的依赖
- dep：管理依赖
- watcher：控制属于哪一个，是data中的message，还是watch中的message等

### Object.defineProperty()实现数据劫持
**1、js对象属性**

**数据属性：**
- 特性：`configurable、enumerable、writable、value`

**访问器属性：**
- 不包含具体数据数值，通常使用`set()`、`get()`操作（`getter()`、`setter()`函数是访问器属性默认的隐藏函数）
- 特性：`configurable、enumerable、get、set`


被Object.defineProperty绑定过的对象，会变成「响应式」化。也就是改变这个对象的时候会触发get和set事件。进而触发一些视图更新。

**2、 `object.defineProperty()`实现对象属性的监听**

```javascript
var o = {};

o.a = 1;
// 等同于：
Object.defineProperty(o, "a", {
  value: 1,
  writable: true,
  configurable: true,
  enumerable: true
});


// 另一个例子，
Object.defineProperty(o, "a", { value : 1 });
// 等同于：
Object.defineProperty(o, "a", {
  value: 1,
  writable: false,
  configurable: false,
  enumerable: false
});
```

例子：使用object.defineProperty()监听数据变化，当修改的对象属性值相同时报错

```javascript
var object = {
    name:'whl',
    age:34
}
function changeIt(object) {

    function descripterFun(value) {
        return {
            enumerable: true,
            get: function () {
                console.log('get it');
                // console.log(this)
                return value;
            },
            // 属性被修改时调用
            set: function (newvalue) {
                console.log('set it ',value,newvalue);
                // console.log(this)
                // if (value === newvalue) {
                //     console.log('value is same')
                // }

                // 抛出自定义错误
                try {
                    if (value === newvalue){
                        throw 'value is same'
                    }

                }catch (e) {
                    console.error(e)
                }
                value = newvalue;
            }
        }
    }
    for(var i in object){
        Object.defineProperty(object, i, descripterFun(object[i]))
    }
}
changeIt(object);
// console.log(object.name);
object.name = 'lhq';
// console.log(object.name);
object.name = 'lhq';
// console.log(object.name);
```
- `Object.defineProperty()`绑定过的对象，会变成「响应式」化。也就是改变这个对象的时候会触发get和set事件，进而触发一些视图更新。

运行结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201025160958101.png#pic_center)
参考博客：[Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)、[深入浅出Object.defineProperty()](https://www.jianshu.com/p/8fe1382ba135)

### Observer：数据监听器
Vue中用Observer类来管理上述响应式化`Object.defineProperty`的过程。

数据劫持、数据代理、数据监听是一个意思。

### Compile：指令编译器

### Watcher：观察者，对视图进行更新

### Dep：收集Watcher，并通知观察者更新
如何收集依赖，通过`Object.defineProperty`，因为读取某个属性会触发get方法，改变某个属性会触发set方法。

所谓依赖，即watcher。


```javascript
function defineReactive (obj, key, val) {
    let Dep; // 依赖

    Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get: () => {
            console.log('我被读了，我要不要做点什么好?');
            // 被读取了，将这个依赖收集起来
            Dep.depend(); // 本次新增
            return val;
        },
        set: newVal => {
            if (val === newVal) {
                return;
            }
            val = newVal;
            // 被改变了，通知依赖去更新
            Dep.notify(); // 本次新增
            console.log("数据被改变了，我要把新的值渲染到页面上去!");
        }
    })
}
```

发布订阅模式：数据发生变化时，通知需要更新的视图部分，并进行更新。

**总结：**
- 在数据被读的时候，触发get方法，执行Dep来收集依赖，也就是收集Watcher。

- 在数据被改的时候，触发set方法，通过对应的所有依赖(Watcher)，去执行更新。

### 面试题

如果绑定了一个数组，但不能被渲染该怎么办？

