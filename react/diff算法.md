> [React原理：通俗易懂的 Fiber](https://juejin.cn/post/7229552637229498405)、[React原理：通俗易懂的 diff 算法](https://juejin.cn/post/7229598397250109497)

### 1 新老架构diff对比

React 16 之前，React 是直接通过 `递归遍历 VDOM 树` 查找不同，对有变化的部分重新生成真实DOM。

React 16之后，引入了 Fiber 架构，在 Reconciler（协调器）中会进行 diff 算法，流程大致如下：

- 第一次渲染时，不需要 Diff 。直接将 `VDOM 转 Fiber`，内存中构建 workInProgressFiber 树，构建完之后，用它替换 currentFiber，再通知 Renderer（渲染器）渲染。
- 后续更新渲染时，会将`新生成的 VDOM 和 旧的 Fiber 进行对比`，决定生成怎样的新的 Fiber（能复用的节点复用，多余的删除，新增的新增）。完成后对新生成的 Fiber 进行 DOM 操作。

### 2 过程

比如现在有一个父节点，它的子节点为 A、B、C、D，那么生成的 VDOM 如下：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/efe96f118a5646c2bfb8cd6cf7d118ad~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

因为是`第一次渲染`，被 Reconciler 直接转成 Fiber：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e1b8a0e3ee4c48f591c7095304971145~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

如果现在组件更新，新的 VDOM 是 A、C、E、F 后，如何处理呢？

**第一轮遍历：**

`线性一一对比`，当前 新VDOM 的节点A 和 旧的 Fiber 中的节点A 可以复用，打上**更新**标记，然后继续遍历 新VDOM 的下一节点C，发现节点C 和 Fiber 中的 节点B 不能复用，结束遍历。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bedd077e468c445ebd3bda7201b6a1d8~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

此时 新VDOM 的节点还没遍历完，则进行第二次遍历。

**第二轮遍历：**

- 接下来，把`旧的 Fiber 中，剩下的 节点 B、C、D 放入一个 Map`，key 就是节点的 key。

- 继续遍历 新VDOM 中剩下的节点，`同样去找能不能复用的节点`。比如发现只有 C 节点能在 Map 中找到，则打上**更新**标记。

遍历完毕后：

- Map 中剩下的 B、D 节点打上**删除**标记。

- 新VDOM 中的 E、F 节点打上**新增**标记。

图解如下： ![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33f2e777be95439ab00e6aa9b8d0571d~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)



















知道了节点如何变化后，生成新的 Fiber 如下：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3202ba5759044a848d74007db72f72bf~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp)

至此，我们完成了 Diff。

### 3 总结

diff的关键点：**查找复用节点**。

第一轮遍历时，线性一一对比，如果新的VDOM节点和旧的Fiber节点不能复用的时候，则终止遍历。

第二轮遍历时，将旧的Fiber剩余的节点放在一个map中，新的VDOM节点与之进行比对，如果新的VDOM节点可以复用，则打上**更新**标识。遍历结束之后，将map中剩余的节点打上**删除**标识，新的VDOM节点剩余的节点打上**新增**标识。

最后根据变化生成新的Fiber节点，然后渲染。