### vue2与vue3的区别

#### 谈谈对vue的理解

##### 核心特性

- 响应式原理，数据驱动视图mvvm
- 组件化开发
- 指令系统
  - 指令 (Directives) 是带有 v- 前缀的特殊属性作用：当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM

##### 与jQuery区别

- 双向绑定使dom节点跟视图绑定后，通过修改**变量**的值控制dom节点的各类属性
- Jquery直接操作DOM元素

##### 与react区别

#### vue3的变化

- 速度更快
- 体积减少
- 更易维护
- 更接近原生
- 更易使用

##### 速度更快

- 重写了虚拟`Dom`实现
- 编译模板的优化
- 更高效的组件初始化
- `undate`性能提高1.3~2倍
- `SSR`速度提高了2~3倍

##### 体积更小

通过`webpack`的`tree-shaking`功能，可以将无用模块“剪辑”，仅打包需要的。

能够`tree-shaking`，有两大好处：

- 对开发人员，能够对`vue`实现更多其他的功能，而不必担忧整体体积过大
- 对使用者，打包出来的包体积变小了

##### 更易维护

###### compositon Api

- 可与现有的`Options API`一起使用
- 灵活的逻辑组合与复用
- `Vue3`模块可以和其他框架搭配使用

![img](https://static.vue-js.com/6f67a590-5088-11eb-85f6-6fac77c0c9b3.png)

简单使用：

```javascript
export default {
    setup() {
        const count = ref(0)
        const double = computed(() => count.value * 2)
        function increment() {
            count.value++
        }
        onMounted(() => console.log('component mounted!'))
        return {
            count,
            double,
            increment
        }
    }
}
```

###### 更好的Typescript支持

`VUE3`是基于`typescipt`编写的，可以享受到自动的类型定义提示。

###### 编译器重写

##### 更接近原生

可以自定义渲染API。

##### 更易使用

响应式 `Api` 暴露出来。

##### 还有一些非兼容性变更

#### vue3的优化方案

##### 源码

###### 源码管理

vue3整个源码是通过 monorepo 的方式维护的，根据功能将不同的模块拆分到packages目录下面不同的子目录中。

![img](https://static.vue-js.com/d7c32520-5c58-11eb-ab90-d9ae814b240d.png)

###### typescript

Vue3是基于typeScript编写的，提供了更好的类型检查，能支持复杂的类型推导。

##### 性能

###### 体积优化

相比Vue2，Vue3整体体积变小了，除了移出一些不常用的API，再重要的是Tree shanking。

任何一个函数，如ref、reavtived、computed等，仅仅在用到的时候才打包，没用到的模块都被摇掉，打包的整体体积变小。

###### 编译优化

**（1）diff算法优化**

vue3在diff算法中相比vue2增加了静态标记。

关于这个静态标记，其作用是为了会发生变化的地方添加一个flag标记，下次发生变化的时候直接找该地方进行比较。

**（2）静态提升**

Vue3中对不参与更新的元素，会做静态提升，只会被创建一次，在渲染时直接复用。

**（3）事件监听缓存**

**（4）SSR优化**

###### 数据劫持优化

在vue2中，数据劫持是通过Object.defineProperty，这个 API 有一些缺陷，并不能检测对象属性的添加和删除（是通过set、delete来解决这个问题的）。

同时在面对嵌套层级比较深的情况下，就存在性能问题。

vue3是通过proxy监听整个对象：
- 可以监听动态属性的添加
- 可以监听删除属性
- 可以监听到数组的索引和数组length属性

##### 语法API

`composition API`优点：
- 优化逻辑组织
  - 相同功能的代码编写在一块
- 优化逻辑复用
  - 在vue2中，我们是通过mixin实现功能混合，如果多个mixin混合，会存在两个非常明显的问题：命名冲突和数据来源不清晰
  - 而通过composition这种形式，可以将一些复用的代码抽离出来作为一个函数，只要的使用的地方直接进行调用即可
