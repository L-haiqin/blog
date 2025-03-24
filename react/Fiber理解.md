一种协调调度算法。fiber是一个js对象，代表组件或dom元素的虚拟节点。

### **1 原因/作用**

- **支持异步可中断更新**：react 16之前的版本使用的是栈协调器（Stack Reconciler），一旦开始更新就无法中断，可能导致主线程长时间阻塞，造成界面卡顿。fiber支持异步可中断更新。
- **实现优先级调度**：Fiber 为不同任务分配优先级（如用户输入 > 动画 > 数据加载），通过调度器（Scheduler）动态调整执行顺序，确保高优先级任务快速响应。例如，用户输入会中断低优先级的渲染任务，优先执行以提升流畅度。
- **增量渲染与时间切片（Time Slicing）**：Fiber 将长时间任务分割成多个“时间片”，每片控制在 5ms 左右，利用浏览器的空闲时间（`requestIdleCallback`）执行，避免阻塞 UI 线程。
- **支持并发模式（Concurrent Mode）**：允许同时处理多个任务。



### **2 核心原理**

- **fiber节点：**每一个组件返回的jsx都会被转换成一个fiber对象，用于存放组件的信息。

- ```js
  {
    type: 'div',          // 组件类型
    stateNode: DOM节点,    // 关联的 DOM 元素
    child: 第一个子节点,    // 子节点链表
    sibling: 兄弟节点,      // 同级节点链表
    return: 父节点,         // 父节点
    expirationTime: 优先级 // 任务优先级
  }
  ```

- **链表树结构**：Fiber 树通过 `child`、`sibling`、`return` 指针形成链表结构，替代原有的树形递归遍历，支持中断后快速定位恢复点.

- **双树结构**：React 维护两棵 Fiber 树——`current`（当前视图）和 `workInProgress`（待更新视图）。更新时复用旧树的 Fiber 节点构建新树，完成后直接替换，减少 DOM 操作

- **协调阶段（Reconciliation）**：可以随时中断，遍历 Fiber 树，对比新旧节点生成副作用列表（Effect List），标记需要更新的节点（如插入、删除）；优先级控制，通过 `expirationTime` 标识任务优先级，调度器根据浏览器空闲时间选择执行顺序

- **提交阶段（Commit）**：此阶段不可以中断，会将协调阶段生成的副作用列表一次性提交到 DOM进行渲染视图。

- **调度器（Scheduler）**：基于 `requestIdleCallback` 实现，将任务拆分为时间片，在浏览器空闲时执行。任务优先级。循环处理任务



### **3 React 的任务调度是怎么做的？**

- **任务分片与优先级**：
  - 时间分片：将任务拆分为 5ms 左右的小块，每次执行后检查剩余时间。若时间耗尽，暂停任务并让出主线。
  - 优先级队列：任务按优先级（如用户交互 > 动画 > 数据加载）排序，高优先级任务可中断低优先级任。
- **调度流程：**
  - 任务入队：更新任务根据优先级加入对应队列（如 `taskQueue` 和 `timerQueue`）。
  - 执行控制：调度器通过循环处理任务，每次执行前检查浏览器空闲时间和任务优先级。若剩余时间不足，记录中断点并等待下一帧恢复。
  - 中断恢复：使用全局指针（如 `workInProgressRoot`）记录当前处理节点，恢复时从中断点继续遍历。



### **4 React 中怎么判断出来浏览器剩余多少空间时间？**

React 通过 **Scheduler 调度器**结合浏览器 API 实现：

- **原生 API 模拟**：使用 `requestIdleCallback` 的 `deadline.timeRemaining()` 获取当前帧剩余时间（约 5ms）。或者通过 `MessageChannel` 或 `setTimeout` 模拟类似行。
- **时间分片控制**：每个任务执行前计算时间预算，若超出则暂停并等待下一帧。



### **5 requestAnimationFrame 了解过吗？它的使用场景是什么？**

`requestAnimationFrame` 是优化动画和渲染的核心工具，适用于需要与浏览器刷新率同步的场景。

使用场景：

- 动画：实现元素位移、旋转等平滑动画（优于 `setTimeout`/`setInterval`）。
- 游戏开发：与游戏引擎配合，确保画面渲染与用户输入同步。
- 数据可视化：动态更新图表或复杂图形（如 Canvas/SVG 渲染。



### **6 React组件 return JSX，这个需要在编译的时候转化才能运行，在编译阶段会被转义成什么 JS 代码？**

JSX 在编译阶段会被 Babel 或 TypeScript 转译为标准的 JavaScript 函数调用，核心逻辑是生成 **React 元素对象**（React Element）。

```js
// JSX 代码
const element = <div className="title">Hello</div>;

// 编译后
const element = React.createElement(
  'div', 
  { className: 'title' }, 
  'Hello'
);

// js对象
{
  $$typeof: Symbol(react.element),
  type: 'div',
  props: { className: 'title', children: 'Hello' }
}
```



### **7 react 优化需要手动优化，有没有一些方案可以自动处理这个问题？**

手动优化：

- memo
- useMemo，useCallback

自动优化：

- 内置的diff算法
- React Compiler 合并条件分支