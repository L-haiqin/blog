### 1 vue初始化

```javascript
// 处理组件配置项
if (options && options._isComponent) {
  // optimize internal component instantiation
  // since dynamic options merging is pretty slow, and none of the
  // internal component options needs special treatment.
  // 子组件：性能优化，减少运行时的原型链查找，提高执行效率
  initInternalComponent(vm, options)
} else {
  // 根组件：选项合并，将全局配置选项合并到根组件的局部配置上
  vm.$options = mergeOptions(
    resolveConstructorOptions(vm.constructor),
    options || {},
    vm
  )
}

// 组件选项合并出现在三个地方：
// 1、vue.component()， 全局api
// 2、{component : {xxx}}子组件内部的时候，compile
// 3、根组件

// expose real self
vm._self = vm

// 组件关系属性的初始化，比如$parent $child $root
initLifecycle(vm)
// 初始化自定义事件
initEvents(vm)
// 解析组件的插槽信息，得到 vm.$slot，处理渲染函数，得到 vm.$createElement 方法，即 h 函数
initRender(vm)
// 调用 beforeCreate 钩子函数
callHook(vm, 'beforeCreate')
// 初始化组件的 inject 配置项，得到 result[key] = val 形式的配置对象，然后对结果数据进行响应式处理，并代理每个 key 到 vm 实例
initInjections(vm) // resolve injections before data/props
// 数据响应式的重点，处理 props、methods、data、computed、watch等
initState(vm)
// 解析组件配置项上的 provide 对象，将其挂载到 vm._provided 属性上
initProvide(vm) // resolve provide after data/props
// 调用 created 钩子函数
callHook(vm, 'created')

// 如果发现配置项上有 el 选项，则自动调用 $mount 方法，也就是说有了 el 选项，就不需要再手动调用 $mount，反之，没有 el 则必须手动调用 $mount
if (vm.$options.el) {
  // 调用 $mount 方法，进入挂载阶段
  vm.$mount(vm.$options.el)
}
```

#### **问：Vue 的初始化过程（new Vue(options)）都做了什么？**

**答：**

- 处理组件配置项
  - 初始化根组件时进行了选项合并操作，将全局配置合并到根组件的局部配置上
  - 初始化每个子组件时做了一些性能优化，将组件配置对象上的一些深层次属性放到 vm.$options 选项中，以提高代码的执行效率
- 初始化组件实例的关系属性，比如 $parent、$children、$root、$refs 等
- 处理自定义事件
- 解析组件的插槽信息，得到 vm.$slot，处理渲染函数，得到 vm.$createElement 方法，即 h 函数
- 调用 beforeCreate 钩子函数
- 初始化组件的 inject 配置项，得到 `ret[key] = val` 形式的配置对象，然后对该配置对象进行响应式处理，并代理每个 key 到 vm 实例上
- 数据响应式，处理 props、methods、data、computed、watch 等选项
- 解析组件配置项上的 provide 对象，将其挂载到 vm._provided 属性上
- 调用 created 钩子函数
- 如果发现配置项上有 el 选项，则自动调用 $mount 方法，也就是说有了 el 选项，就不需要再手动调用 $mount 方法，反之，没提供 el 选项则必须调用 $mount
- 接下来则进入挂载阶段

### 2 vue响应式原理

```javascript
/**
 * 两件事：
 *   数据响应式的入口：分别处理 props、methods、data、computed、watch
 *   优先级：props、methods、data、computed 对象中的属性不能出现重复，优先级和列出顺序一致
 *         其中 computed 中的 key 不能和 props、data 中的 key 重复，methods 不影响
 */
export function initState (vm: Component) {
  vm._watchers = []
  const opts = vm.$options
  // 处理 props 对象，为 props 对象的每个属性设置响应式，并将其代理到 vm 实例上
  if (opts.props) initProps(vm, opts.props)
  // 处理 methos 对象，校验每个属性的值是否为函数、和 props 属性比对进行判重处理，最后得到 vm[key] = methods[key]
  if (opts.methods) initMethods(vm, opts.methods)
  /**
   * 做了三件事
   *   1、判重处理，data 对象上的属性不能和 props、methods 对象上的属性相同
   *   2、代理 data 对象上的属性到 vm 实例
   *   3、为 data 对象的上数据设置响应式 
   */
  if (opts.data) {
    initData(vm)
  } else {
    observe(vm._data = {}, true /* asRootData */)
  }
  /**
   * 三件事：
   *   1、为 computed[key] 创建 watcher 实例，默认是懒执行
   *   2、代理 computed[key] 到 vm 实例
   *   3、判重，computed 中的 key 不能和 data、props 中的属性重复
   */
  if (opts.computed) initComputed(vm, opts.computed)
  /**
   * 三件事：
   *   1、处理 watch 对象
   *   2、为 每个 watch.key 创建 watcher 实例，key 和 watcher 实例可能是 一对多 的关系
   *   3、如果设置了 immediate，则立即执行 回调函数
   */
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
    
  /**
   * 其实到这里也能看出，computed 和 watch 在本质是没有区别的，都是通过 watcher 去实现的响应式
   * 非要说有区别，那也只是在使用方式上的区别，简单来说：
   *   1、watch：适用于当数据变化时执行异步或者开销较大的操作时使用，即需要长时间等待的操作可以放在 watch 中
   *   2、computed：其中可以使用异步方法，但是没有任何意义。所以 computed 更适合做一些同步计算
   */
}
```

#### **问：Vue 响应式原理是怎么实现的？**

**答**：

- 响应式的核心是通过 `Object.defineProperty` 拦截对数据的访问和设置
- 响应式的数据分为两类：
  - 对象，循环遍历对象的所有属性，为每个属性设置 getter、setter，以达到拦截访问和设置的目的，如果属性值依旧为对象，则递归为属性值上的每个 key 设置 getter、setter
    - 访问数据时（obj.key)进行依赖收集，在 dep 中存储相关的 watcher
    - 设置数据时由 dep 通知相关的 watcher 去更新
  - 数组，增强数组的那 7 个可以更改自身的原型方法，然后拦截对这些方法的操作
    - 添加新数据时进行响应式处理，然后由 dep 通知 watcher 去更新
    - 删除数据时，也要由 dep 通知 watcher 去更新
    -  7个方法：push、pop、shift、unshift、splice、sort、reverse

### 3 异步更新原理

通过 `Object.defineProperty` 为对象的每个 key 设置 getter、setter，从而拦截对数据的访问和设置。

当对数据进行更新操作时，比如 `obj.key = 'new val'` 就会触发 setter 的拦截，从而检测新值和旧值是否相等，如果相等什么也不做，如果不相等，则更新值，然后由 `dep` 通知 `watcher` 进行更新。所以，`异步更新` 的入口点就是 setter 中最后调用的 `dep.notify()` 方法。

> /src/core/observer/index.js

```javascript
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    // 拦截 obj.key
    get: function reactiveGetter () {
      const value = getter ? getter.call(obj) : val
      if (Dep.target) {
        // 依赖收集，将dep添加到watcher中，也将watcher添加到dep中
        dep.depend()
        if (childOb) {
          childOb.dep.depend()
          if (Array.isArray(value)) {
            // 对数组进行依赖收集
            dependArray(value)
          }
        }
      }
      return value
    },
    // 拦截 obj.key = newVal 的操作
    set: function reactiveSetter (newVal) {
      // 获取老值
      const value = getter ? getter.call(obj) : val
      /* eslint-disable no-self-compare */
      if (newVal === value || (newVal !== newVal && value !== value)) {
        return
      }
      /* eslint-enable no-self-compare */
      if (process.env.NODE_ENV !== 'production' && customSetter) {
        customSetter()
      }
      // #7981: for accessor properties without setter
      if (getter && !setter) return
      // 用新值替换老值
      if (setter) {
        setter.call(obj, newVal)
      } else {
        val = newVal
      }
      // 对新值做响应式处理
      childOb = !shallow && observe(newVal)
      // 当响应式数据更新时，通知依赖通知更新
      dep.notify()
    }
  })
```

> /src/core/observer/dep.js

```javascript
/**
 * 通知 dep 中的所有 watcher，执行 watcher.update() 方法
 */
notify () {
  // stabilize the subscriber list first
  const subs = this.subs.slice()
  // 遍历 dep 中存储的 watcher，执行 watcher.update()
  for (let i = 0, l = subs.length; i < l; i++) {
    subs[i].update()
  }
}

```

#### **问：Vue 的异步更新机制是如何实现的？**

**答**：

Vue 的异步更新机制的核心是利用了**浏览器的异步任务队列**来实现的，首选**微任务队列，宏任务队列**次之。

当响应式数据更新后，会调用 dep.notify 方法，通知 dep 中收集的 watcher 去执行 update 方法，watcher.update 将 watcher 自己放入一个 watcher 队列（全局的 queue 数组）。

然后通过 nextTick 方法将一个刷新 watcher 队列的方法（flushSchedulerQueue）放入一个全局的 callbacks 数组中。

如果此时浏览器的异步任务队列中没有一个叫 flushCallbacks 的函数，则执行 timerFunc 函数，将 flushCallbacks 函数放入异步任务队列。如果异步任务队列中已经存在 flushCallbacks 函数，等待其执行完成以后再放入下一个 flushCallbacks 函数。

flushCallbacks 函数负责执行 callbacks 数组中的所有 flushSchedulerQueue 函数。

flushSchedulerQueue 函数负责刷新 watcher 队列，即执行 queue 数组中每一个 watcher 的 run 方法，从而进入更新阶段，比如执行组件更新函数或者执行用户 watch 的回调函数。

#### **问：Vue 的 nextTick API 是如何实现的？**

**答**：

Vue.nextTick 或者 vm.$nextTick 的原理其实很简单，就做了两件事：

- 将传递的回调函数用 `try catch` 包裹然后放入 callbacks 数组
- 执行 timerFunc 函数，在浏览器的异步任务队列放入一个 flushCallbacks 数组的函数（首选promise.then，再setImmediate，最后setTimeout）

```javascript
// /src/core/util/next-tick.js

const callbacks = []
let pending = false

/**
 * 完成两件事：
 *   1、用 try catch 包装 flushSchedulerQueue 函数，然后将其放入 callbacks 数组
 *   2、如果 pending 为 false，表示现在浏览器的任务队列中没有 flushCallbacks 函数
 *     如果 pending 为 true，则表示浏览器的任务队列中已经被放入了 flushCallbacks 函数，
 *     待执行 flushCallbacks 函数时，pending 会被再次置为 false，表示下一个 flushCallbacks 函数可以进入
 *     浏览器的任务队列了
 * pending 的作用：保证在同一时刻，浏览器的任务队列中只有一个 flushCallbacks 函数
 * @param {*} cb 接收一个回调函数 => flushSchedulerQueue
 * @param {*} ctx 上下文
 * @returns 
 */
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  // 用 callbacks 数组存储经过包装的 cb 函数
  callbacks.push(() => {
    if (cb) {
      // 用 try catch 包装回调函数，便于错误捕获
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    // 执行 timerFunc，在浏览器的任务队列中（首选微任务队列）放入 flushCallbacks 函数
    timerFunc()
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```

Vue 在更新 DOM 时是**异步**执行的。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。

[Vue.nextTick( [callback, context] )](https://cn.vuejs.org/v2/api/#Vue-nextTick)用法：

在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

应用场景：一般用于 **`this.key = newVal` 更改数据后，想立即获取更改过后的 DOM 数据。**

```javascript
// 修改数据
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})

// 作为一个 Promise 使用，因为 $nextTick() 返回了一个 Promise 对象
Vue.nextTick()
  .then(function () {
    // DOM 更新了
  })
```

### 4 全局API 

API官方文档：https://cn.vuejs.org/v2/api/#%E5%85%A8%E5%B1%80-API

- [Vue.extend](https://cn.vuejs.org/v2/api/#Vue-extend)
- [Vue.nextTick](https://cn.vuejs.org/v2/api/#Vue-nextTick)
- [Vue.set](https://cn.vuejs.org/v2/api/#Vue-set)
- [Vue.delete](https://cn.vuejs.org/v2/api/#Vue-delete)
- [Vue.directive](https://cn.vuejs.org/v2/api/#Vue-directive)
- [Vue.filter](https://cn.vuejs.org/v2/api/#Vue-filter)
- [Vue.component](https://cn.vuejs.org/v2/api/#Vue-component)
- [Vue.use](https://cn.vuejs.org/v2/api/#Vue-use)
- [Vue.mixin](https://cn.vuejs.org/v2/api/#Vue-mixin)
- [Vue.compile](https://cn.vuejs.org/v2/api/#Vue-compile)
- [Vue.observable](https://cn.vuejs.org/v2/api/#Vue-observable)
- [Vue.version](https://cn.vuejs.org/v2/api/#Vue-version)

#### **问**：Vue.set(target, key, val) 做了什么

**答**：

由于 Vue 无法探测普通的新增 property (比如 this.myObject.newProperty = 'hi')，所以通过 Vue.set 为向响应式对象中添加一个 property，可以确保这个新 property 同样是响应式的，且触发视图更新。

- 更新数组指定下标的元素：Vue.set(array, idx, val)，内部通过 **splice** 方法实现响应式更新
- 更新对象已有属性：Vue.set(obj, key ,val)，直接更新即可 => `obj[key] = val`
- 不能向 Vue 实例或者 $data 动态添加根级别的响应式数据
- Vue.set(obj, key, val)，如果 obj 不是响应式对象，会执行 `obj[key] = val`，但是不会做响应式处理
- Vue.set(obj, key, val)，为响应式对象 obj 增加一个新的 key，则通过 **defineReactive** 方法设置响应式，并触发依赖更新

#### 问：Vue.use(plugin) 做了什么？

**答**：

负责安装 plugin 插件，其实就是执行插件提供的 install 方法。

- 首先判断该插件是否已经安装过；
- 如果没有，则执行插件提供的 install 方法安装插件，具体做什么有插件自己决定。

**用法**：

安装 Vue.js 插件。如果插件是一个对象，必须提供 `install` 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。

该方法需要在调用 `new Vue()` 之前被调用。

当 install 方法被同一个插件多次调用，插件将只会被安装一次。

#### **问**：Vue.mixin(options) 做了什么？

**答**：

负责在 Vue 的全局配置上合并 options 配置。然后在每个组件生成 vnode 时会将全局配置合并到组件自身的配置上来。

- 标准化 options 对象上的 props、inject、directive 选项的格式
- 处理 options 上的 extends 和 mixins，分别将他们合并到全局配置上
- 然后将 options 配置和全局配置进行合并，选项冲突时 options 配置会覆盖全局配置

**用法**：

全局注册一个混入，影响注册之后所有创建的每个 Vue 实例。插件作者可以使用混入，向组件注入自定义的行为。**不推荐在应用代码中使用**。

### 5 实例方法

API官方文档：https://cn.vuejs.org/v2/api/#%E5%AE%9E%E4%BE%8B-property

- [vm.$data](https://cn.vuejs.org/v2/api/#vm-data)
- [vm.$props](https://cn.vuejs.org/v2/api/#vm-props)
- [vm.$el](https://cn.vuejs.org/v2/api/#vm-el)
- [vm.$options](https://cn.vuejs.org/v2/api/#vm-options)
- [vm.$parent](https://cn.vuejs.org/v2/api/#vm-parent)
- [vm.$root](https://cn.vuejs.org/v2/api/#vm-root)
- [vm.$children](https://cn.vuejs.org/v2/api/#vm-children)
- [vm.$slots](https://cn.vuejs.org/v2/api/#vm-slots)
- [vm.$scopedSlots](https://cn.vuejs.org/v2/api/#vm-scopedSlots)
- [vm.$refs](https://cn.vuejs.org/v2/api/#vm-refs)
- [vm.$isServer](https://cn.vuejs.org/v2/api/#vm-isServer)
- [vm.$attrs](https://cn.vuejs.org/v2/api/#vm-attrs)
- [vm.$listeners](https://cn.vuejs.org/v2/api/#vm-listeners)
- [vm.$watch](https://cn.vuejs.org/v2/api/#vm-watch)
- [vm.$set](https://cn.vuejs.org/v2/api/#vm-set)
- [vm.$delete](https://cn.vuejs.org/v2/api/#vm-delete)
- [vm.$on](https://cn.vuejs.org/v2/api/#vm-on)
- [vm.$once](https://cn.vuejs.org/v2/api/#vm-once)
- [vm.$off](https://cn.vuejs.org/v2/api/#vm-off)
- [vm.$emit](https://cn.vuejs.org/v2/api/#vm-emit)

讲解：[Vue 源码解读（6）—— 实例方法](https://juejin.cn/post/6953503236254859294#heading-0)

### 6 编译器

#### 问：简单说一下 Vue 的编译器都做了什么？

**答**：

Vue 的编译器做了三件事情：

- 将组件的 html 模版解析成 AST 对象
- 优化，遍历 AST，为每个节点做静态标记，标记其是否为静态节点，然后进一步标记出静态根节点，这样在后续更新的过程中就可以跳过这些静态节点了；标记静态根用于生成渲染函数阶段，生成静态根节点的渲染函数
- 从 AST 生成运行时的渲染函数，即大家说的 render，其实还有一个，就是 staticRenderFns 数组，里面存放了所有的静态节点的渲染函数

### 7 patch（diff算法）



