### 父子组件的生命周期执行顺序

---

- parent before create

- **parent created**

- parent before mount

- child before create

- **child created**

- child before mount

- **child mounted**

- **parent mounted**

父亲初始化 -> 父亲渲染完毕 -> 孩子初始化&挂载完毕 -> 父亲挂载完毕

- parent before update（以下执行顺序所在的情况：父亲需要把数据传递给孩子的情况）

- child before update

- **child updated**

- **parent updated**

### 父子组件通信

---

1、父组件传递数据给子组件，子组件使用props接收父组件中定义的参数

步骤：

- 首先找到**父组件中使用子组件的地方**，为其**添加上父组件需要传递给子组件的数据**
- 再在子组件中**使用props接收**参数

其他：

- 可以传递静态的值或者动态的值

  - ```javascript
    // 传递静态的值（父组件中写）
    <blog-post title="My journey with Vue"></blog-post>
    
    // 通过 v-bind 传递动态的值（父组件中写）
    <blog-post v-bind:title="post.title"></blog-post>
    ```

- 可以传入一个数字/布尔值/数组/对象/对象的所有property

- 在子组件中用props接受参数，并标明类型

  - ```javascript
    // 接受父组件传递的数据（子组件中写）
    props:{
    title:String
    }
    ```

  - props的更多用法：[prop-vue.js](https://cn.vuejs.org/v2/guide/components-props.html)

2、子组件传递数据给父组件，子组件使用 $emit(事件名,参数···) 方法将数据传递给父组件

步骤：

- 子组件中使用**$emit方法**去编写代码，触发父组件中的event
- 之后在父组件中实现对应的dom事件

其他：

- $emit方法：
  - ![img](https://img2020.cnblogs.com/i-beta/774496/202003/774496-20200305151315128-2071168637.png)

  - 事件名称eventName对应的事件是在**父组件中通过v-on监听的一个native DOM事件**。当在子组件中执行$emit(eventName)时，就会触发父组件中对应的event。（事件名必须一致）

  - ```html
    // 子组件中
    methods:{
    		add(){
    			this.$emit('add',this.title); // 子组件向父组件传递数据
    			
    			eventBus.$emit('addItem',this.title); // 把title传出去给eventBus
    		}
    	}
    
    // 父组件中
    <MyInput @add = 'add'></MyInput>
    methods:{
    	  add(title){
    		  this.list.push({
    			  id: Math.random(),
    			  title
    		  })
    	  }
      }
    ```

参考博客：[Vue 父子组件通信](https://www.cnblogs.com/HouJiao/p/12421851.html)

### 兄弟组件通信

---

通过创建一个vue的实例evenBus（中央事件总线），其拥有$emit和$on方法来实现兄弟组件通信。

步骤：

- 新建event-bus.js文件，里面new一个vue的实例

  - ```javascript
    import Vue from 'vue'
    const evenBus = new Vue();
    export default evenBus;
    ```

- MyInput组件里面通过$emit方法将title传递出去

  - ```javascript
  	methods:{
  		add(){
  			this.$emit('add',this.title); // 子组件向父组件传递数据
  			
  			eventBus.$emit('addItem',this.title); // 把title传出去给eventBus
  		}
  	}
    ```

- MyList组件里面通过$on方法接受title，使用即可

  - ```javascript
    methods:{
  	  handleAddTitle(title){
  		  console.log('title:',title)
  	  }
    },
    mounted(){
  	  console.log("child mount");
  	  
  	  // 给eventBus监听一个addItem事件，如果触发了这个事件，则执行handleAddTitle函数
  	  eventBus.$on('addItem',this.handleAddTitle);
    },
    ```

其他：

- $emit('事件名', 要传送的数据)：触发当前实例上的时间，要传递的数据会传给监听器
- $on('事件名',callback) ：callback回调$emit要传送的数据，监听当前实例上的自定义事件

### 自定义组件的v-model

---

参考哈默视频。

vue 中 v-model 是一种语法糖，它等于 **props + 事件回调更改**

```html
<input  v-model="someValue"  />  

<--等价于如下:-->

<input  :value="someValue" @input="something = $event.target.value" />
```

注意：通过事件去更改父组件中数据，而不是直接在子组件中直接更改 父组件的数据。

### v-for列表渲染

---

1、唯一标识key

以便跟踪每个节点的身份，从而重用和重新排序现有元素。

```html
<li v-for = "(value, key, index) in object" :key='id'>
    {{ index }} - {{ key }} - {{ value }}
</li>
```

2、v-for和v-if不可以一起使用

当它们处于**同一节点，v-for 的优先级比 v-if 更高**，这意味着 v-if 将分别重复运行于每个 v-for 循环中**（即先渲染出v-for，再用v-if去判断是否需要被隐藏掉，在有些情况下（比如先判断再渲染的时候）就会造成性能浪费）**。当你只想为部分项渲染节点时，这种优先级的机制会十分有用，如下：

```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

上面的代码将只渲染未完成的 todo。

而如果你的目的是有条件地跳过循环的执行，那么可以**将 v-if 置于外层元素** (或 `<template>`) 上。如：

```html
// 也可以这样写<template v-if="todos.length"></template>
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```

也可以让v-for在外面，v-if嵌套在里面。

```html
<li v-for="todo in todos">
  <span v-if="todos.length">{{ todo }}</span>
</li>
```

官方文档：[列表渲染](https://cn.vuejs.org/v2/guide/list.html)

### v-show和v-if的区别

---

|                          v-if                          |                            v-show                            |
| :----------------------------------------------------: | :----------------------------------------------------------: |
| 通过添加/删除dom元素（html结构里面不显示未命中的元素） | 设置css属性内联样式display:none;（html结构里面会显示未命中的元素） |
|     适合元素显示/隐藏一次性决定的（避免造成重排）      |                        适合频繁切换的                        |

### vue-router中的route和router区别

---

**1、$route 表示 当前路由信息对象**

表示当前激活的路由的状态信息，包含了当前 URL 解析得到的信息，还有 URL 匹配到的 route records（路由记录）。

路由信息对象：即$router会被注入每个组件中，可以利用它进行一些信息的获取。

```javascript
**1.$route.path**
      字符串，对应当前路由的路径，总是解析为绝对路径，如 "/foo/bar"。
**2.$route.params**
      一个 key/value 对象，包含了 动态片段 和 全匹配片段，
      如果没有路由参数，就是一个空对象。
**3.$route.query**
      一个 key/value 对象，表示 URL 查询参数。
      例如，对于路径 /foo?user=1，则有 $route.query.user == 1，
      如果没有查询参数，则是个空对象。
**4.$route.hash**
      当前路由的 hash 值 (不带 #) ，如果没有 hash 值，则为空字符串。锚点
**5.$route.fullPath**
      完成解析后的 URL，包含查询参数和 hash 的完整路径。
**6.$route.matched**
      数组，包含当前匹配的路径中所包含的所有片段所对应的配置参数对象。
**7.$route.name    当前路径名字**
**8.$route.meta  路由元信息
```

**2、$router 表示 全局的路由实例**

全局的路由实例，是router构造方法的实例。

在 Vue 实例内部，你可以通过 $router 访问路由实例。

**1）路由实例方法push**

```javascript
// 字符串
      this.$router.push('home')
// 对象
      this.$router.push({ path: 'home' })
// 命名的路由
      this.$router.push({ name: 'user', params: { userId: 123 }})
// 带查询参数，变成 /register?plan=123
      this.$router.push({ path: 'register', query: { plan: '123' }})
```

push方法其实和 `<router-link :to="...">` 是等同的。

注意：push方法的跳转会向 history 栈添加一个新的记录，当我们点击浏览器的返回按钮时可以看到之前的页面。

**2）路由实例方法go**

```javascript
// 页面路由跳转 前进或者后退
this.$router.go(-1) // 后退
```

**3）路由实例方法replace**

```javascript
// push方法会向 history 栈添加一个新的记录，而replace方法是替换当前的页面，
// 不会向 history 栈添加一个新的记录
<router-link to="/05" replace>05</router-link>

// 一般使用replace来做404页面
this.$router.replace('/')
```

### vue单向数据流

---

概念：

- 所有的 prop 都使得其父子 prop 之间形成了一个**单向下行绑定**：**父级 prop 的更新会向下流动到子组件中，但是反过来则不行。**这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。

- 额外的，**每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值。**这意味着你不应该在一个子组件内部改变 prop。如果你这样做了，Vue 会在浏览器的控制台中发出警告。

如何在子组件中更改props中的值呢？有如下两种方案：

1）这个 **prop 用来传递一个初始值，这个子组件接下来希望将其作为一个本地的 prop 数据来使用**。在这种情况下，最好定义一个本地的 data 属性并将这个 prop 用作其初始值：

```javascript
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
}
```

2）这个 **prop 以一种原始的值传入且需要进行转换**。在这种情况下，最好使用这个 prop 的值来定义一个计算属性：

```javascript
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 `prop` 来说，在子组件中改变这个对象或数组本身将会影响到父组件的状态。

### vuex状态管理

---

Vuex 的状态存储是**响应式**的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。

几个核心概念：

- state     存放状态
- mutations   state成员操作
- getters     加工state成员给外界
- actions     异步操作
- modules   模块化状态管理

mutation和action的区别：

- Mutation必须是同步函数；action必须是异步函数。
- 更改state的唯一方法是通过提交 mutation；Action中不进行状态的直接更改，而是通过commit触发mutation。
- mutation的触发通过store.commit来进行；action的触发通过store.dispatch进行

视频教程：小野森森B站（一定要自己敲一遍！！！）