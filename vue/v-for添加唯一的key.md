### v-for添加唯一的key

来源：[为什么使用v-for时必须添加唯一的key?](https://www.jianshu.com/p/342e2d587e69#:~:text=%E4%BD%BF%E7%94%A8%20v-for%20%E6%9B%B4%E6%96%B0%E5%B7%B2%E6%B8%B2%E6%9F%93%E7%9A%84%E5%85%83%E7%B4%A0%E5%88%97%E8%A1%A8%E6%97%B6%2C%E9%BB%98%E8%AE%A4%E7%94%A8%20%E5%B0%B1%E5%9C%B0%E5%A4%8D%E7%94%A8,%E7%AD%96%E7%95%A5%3B%E5%88%97%E8%A1%A8%E6%95%B0%E6%8D%AE%E4%BF%AE%E6%94%B9%E7%9A%84%E6%97%B6%E5%80%99%2C%E4%BB%96%E4%BC%9A%E6%A0%B9%E6%8D%AEkey%E5%80%BC%E5%8E%BB%E5%88%A4%E6%96%AD%E6%9F%90%E4%B8%AA%E5%80%BC%E6%98%AF%E5%90%A6%E4%BF%AE%E6%94%B9%2C%E5%A6%82%E6%9E%9C%E4%BF%AE%E6%94%B9%2C%E5%88%99%E9%87%8D%E6%96%B0%E6%B8%B2%E6%9F%93%E8%BF%99%E4%B8%80%E9%A1%B9%2C%E5%90%A6%E5%88%99%E5%A4%8D%E7%94%A8%E4%B9%8B%E5%89%8D%E7%9A%84%E5%85%83%E7%B4%A0%3B%20%E6%88%91%E4%BB%AC%E5%9C%A8%E4%BD%BF%E7%94%A8%E7%9A%84%E4%BD%BF%E7%94%A8%E7%BB%8F%E5%B8%B8%E4%BC%9A%E4%BD%BF%E7%94%A8%20index%20%28%E5%8D%B3%E6%95%B0%E7%BB%84%E7%9A%84%E4%B8%8B%E6%A0%87%29%E6%9D%A5%E4%BD%9C%E4%B8%BA%20key%2C%E4%BD%86%E5%85%B6%E5%AE%9E%E8%BF%99%E6%98%AF%E4%B8%8D%E6%8E%A8%E8%8D%90%E7%9A%84%E4%B8%80%E7%A7%8D%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95%3B)、[深度剖析：如何实现一个 Virtual DOM 算法](https://github.com/livoras/blog/issues/13)

主要考点是**虚拟dom、diff算法**。

#### 前言

使用`v-for`更新已渲染的元素列表时,默认用`就地复用`策略;列表数据修改的时候,他会根据key值去判断某个值是否修改,如果修改,则重新渲染这一项,否则复用之前的元素;
 我们在使用的使用经常会使用`index`(即数组的下标)来作为`key`,但其实这是不推荐的一种使用方法;

举个🌰

```javascript
const list = [
    {
        id: 1,
        name: 'test1',
    },
    {
        id: 2,
        name: 'test2',
    },
    {
        id: 3,
        name: 'test3',
    },
]

<div v-for="(item, index) in list" :key="index" >{{item.name}}</div>
```

上面这种是我们做项目中常用到的一种场景，因为不加key,vue现在直接报错，所以我使用index作为key；下面列举两种常见的数据更新情况：

#### 1、在最后一条数据后再加一条数据

```javascript
const list = [
    {
        id: 1,
        name: 'test1',
    },
    {
        id: 2,
        name: 'test2',
    },
    {
        id: 3,
        name: 'test3',
    },
    {
        id: 4,
        name: '我是在最后添加的一条数据',
    },
]

```

此时前三条数据直接复用之前的，新渲染最后一条数据，此时用`index`作为`key`，没有任何问题。

#### 2、在中间插入一条数据

```javascript
const list = [
    {
        id: 1,
        name: 'test1',
    },
    {
        id: 4,
        name: '我是插队的那条数据',
    }
    {
        id: 2,
        name: 'test2',
    },
    {
        id: 3,
        name: 'test3',
    },
]
```

此时更新渲染数据，通过`index`定义的`key`去进行前后数据的对比，发现

```javascript
之前的数据                         之后的数据

key: 0  index: 0 name: test1     key: 0  index: 0 name: test1
key: 1  index: 1 name: test2     key: 1  index: 1 name: 我是插队的那条数据
key: 2  index: 2 name: test3     key: 2  index: 2 name: test2
                                 key: 3  index: 3 name: test3
```

除了第一个数据可以复用之前的之外，另外三条数据都需要重新渲染。

最好的办法是使用数组中不会变化的那一项作为`key`值，对应到项目中的id，即每条数据都有一个唯一的`id`，来标识这条数据的唯一性。

使用`id`作为`key`值，我们再来对比一下向中间插入一条数据，此时会怎么去渲染

```javascript
之前的数据                               之后的数据

key: 1  id: 1 index: 0 name: test1     key: 1  id: 1 index: 0  name: test1
key: 2  id: 2 index: 1 name: test2     key: 4  id: 4 index: 1  name: 我是插队的那条数据
key: 3  id: 3 index: 2 name: test3     key: 2  id: 2 index: 2  name: test2
                                       key: 3  id: 3 index: 3  name: test3
```

只要新渲染这一条数据即可，其他都是就复用之前的。

真正的原因是来源于**虚拟DOM的Diff算法**。算法大致流程如下：

![img](https:////upload-images.jianshu.io/upload_images/3297464-ee627869a6714336.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/477)

我们希望可以在B和C之间加一个F，Diff算法默认执行起来是这样的：

![img](https:////upload-images.jianshu.io/upload_images/3297464-d912523aac5fd108.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/572)

即把C更新成F，D更新成C，E更新成D，最后再插入E，是不是很没有效率？ 

所以我们需要使用key来给每个节点做一个唯一标识，Diff算法就可以正确的识别此节点，找到正确的位置区插入新的节点。

![img](https:////upload-images.jianshu.io/upload_images/3297464-650689b4bd4b9eb6.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/452)

所以一句话，key的作用主要是为了**高效的更新虚拟DOM，提升渲染效率**。另外vue中在使用相同标签名元素的过渡切换时，也会使用到key属性，其目的也是为了让vue可以区分它们，否则vue只会替换其内部属性而不会触发过渡效果。

**算法实现 ：**

- 步骤一：用JS对象模拟DOM树
- 步骤二：比较两棵虚拟DOM树的差异
- 步骤三：把差异应用到真正的DOM树上