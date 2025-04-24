### 1 组件间通信

父子组件、兄弟组件、任意组件

props

callback回调函数

ref

context、redux（都属于状态管理）

事件监听EventEmitter（观察者模式）

全局变量，比如window、process（node）

### 2 页面之间的数据传递

context、redux

LocalStorage（存储在本地，一直有效）和SessionStorage（不同的浏览器窗口不共享，关闭浏览器就消失）

url传参

postMessage API

```js
window.postMessage(data, '*');
window.postMessage(data, '*');
```

事件EventEmitter

### 3 触发组件重新渲染的场景

父组件重新渲染

state更新

props更新

context更新

hooks的依赖像更新

### 4 受控组件和非受控组件的区别

处理表单数据的两者不同方式，受控组件的状态变化由组件自身的state或者props控制，非受控组件由dom事件控制（ref）。

受控组件：

- 使用state和onChange的方式，数据实时更新，并且数据存储在组件内部
- 每次输入都会触发状态实时更新和数据渲染，可能影响性能；需要使用useState手动管理状态
- 适用场景：动态表单联动、实时输入验证

非受控组件：

- 使用ref的方式从dom元素获取数据
- 类似传统的html表单元素，数据存储在dom中，而不是组件的内部
- 适用场景：简单表单提交，使用第三方库（JsonEditor，代码编辑，富文本）

推荐：

- 优先使用受控组件，保证数据的准确和可控
- 大型表单对性能要求比较高的话，推荐使用非受控组件

### 5 高阶函数

函数可以作为参数传递，并且返回一个新的函数。

setTimeout

setInterval

防抖，节流

函数柯里化

### 6 高阶组件

组件可以作为参数传递，并且返回一个新的组件的函数。

场景：

- 状态管理、访问权限、错误处理、性能监控、大促限流

现有的：

- memo：减少不必要的渲染
- lazy：按需加载

```js
const MarkdownPreview = lazy(() => import('./MarkdownPreview.js')); // 接受一个返回 import() 动态加载的 Promise 函数
```

- [connect](https://cn.react-redux.js.org/api/connect/)：React-Redux，为组件注入store。可以使用useSelector、useDispatch、useStore

```js
import { connect } from 'react-redux';
const mapState = (state) => ({ user: state.user });
export default connect(mapState)(UserProfile);

// unified-portal例子
const mapState = (state: RootState) => state.app;
const mapDispatch = { toggleSideBar };
const connector = connect(mapState, mapDispatch);

type PropsFromRedux = ConnectedProps<typeof connector>;

function Hamburger(props: PropsFromRedux) {
  const { sidebarCollapsed, toggleSideBar } = props;
  const Icon = sidebarCollapsed ? MenuUnfoldOutlined : MenuFoldOutlined;

  return (
    <div className="hamburger-container">
      <Icon onClick={() => toggleSideBar()} />
    </div>
  );
}

export default connector(Hamburger);
```

- withRouter：React-Router，为组件注入路由对象（`history`、`location`），实现导航控制

```js
import { withRouter } from 'react-router-dom';
const NavButton = withRouter(({ history }) => (
  <button onClick={() => history.push('/home')}>首页</button>
));
```

自定义：

- PermissionContainer、PageContainer（错误、loading）==> 好像是用with开头命名比较多

### 7 页面白屏怎么捕获

使用componentDidCatch和getDerivedStateFromError，封装一个高阶组件，用于通用的错误处理。

```js
function withErrorBoundary(WrappedComponent) {
  return class extends React.Component {
    state = { hasError: false };

    static getDerivedStateFromError() {
      return { hasError: true };
    }

    componentDidCatch(error, info) {
      // 上报错误，接入 Sentry、Bugsnag 等 APM 工具，实现全链路错误追踪
    }

    render() {
      return this.state.hasError 
        ? <FallbackUI /> // 渲染降级ui
        : <WrappedComponent {...this.props} />;
    }
  };
}
```

| 区别         | componentDidCatch                | ge tDerivedStateFromError |
| ------------ | -------------------------------- | ------------------------- |
| **方法类型** | 实例方法（可访问 `this`）        | 静态方法（无 `this`）     |
| **主要用途** | 副作用处理（日志上报、重试逻辑） | 状态更新（渲染降级 UI）   |
| **返回值**   | 无                               | 必须返回状态对象或 `null` |
| **执行阶段** | Commit 阶段（DOM 更新后）        | Render 阶段（DOM 更新前） |
| **优先级**   | 低（若同时定义，后执行）         | 高（先执行状态更新）      |

### 8 hooks的使用注意事项

- 调用顺序：不要在循环、条件或嵌套函数中调用Hook，必须始终在React函数的顶层使用Hook
- 命名问题：必须使用use开头
- 依赖数组管理：依赖项缺失导致无限重复渲染
- 闭包陷阱和值捕获

```js
useEffect(() => {
  const timer = setTimeout(() => {
    console.log(count); // 始终输出初始值，可以改成使用ref存储
  }, 3000);
  return () => clearTimeout(timer);
}, []);
```

- 避免重复创建对象和函数：useCallback、useMemo
- 子组件重复渲染：父组件状态变化导致所有子组件默认重渲染，使用memo

- useEffect副作用清理：比如定时器，调用一些浏览器的api（事件监听）
- 状态管理，初始化状态近生效一次

```js
const [user, setUser] = useState(props.user); // props.user更新时不会同步
// 需要放在useEffect里面
```

**为什么hooks要放在顶部？**

因为react执行代码的时候，会把每一个hooks放在队列里面，有一定的顺序，状态更新的时候是按照顺序来执行的。

**自定义hook的应用场景？**（逻辑处理和代码复用）

- 异步数据请求：接口请求做一些分页的处理
- 与浏览器/第三方sdk的交互：事件监听和定时器；echart实例初始化；请求native sdk；本地存储；媒体播放
- 性能优化：防抖节流

### 9 函数组件和类组件的优缺点/差异

| 区别       | 类组件                                 | 函数组件                                                     |
| ---------- | -------------------------------------- | ------------------------------------------------------------ |
| 定义方式   | 使用class继承React.Component           | 使用普通函数定义                                             |
| 状态管理   | this.setState()和this.state            | useState                                                     |
| 生命周期   | 多个钩子函数，比较全面                 | useEffect（模拟componentDidMount、componentDidUpdate、componentWillUnmount） |
| 事件处理   | 需要绑定this                           | 不需要绑定this                                               |
| 性能优化   | shouldComponentUpdate                  | 使用memo、useMemo、useCallback                               |
| 代码简洁度 | 代码较为冗长，尤其是处理多个生命周期时 | 代码更简洁                                                   |
| 社区趋势   | 逐渐被函数组件替代                     | 主流选择                                                     |

使用场景：

- 推荐使用函数组件
- 如果对生命周期要求比较严格的话，或者一些旧项目，可以考虑类组件

### 10 类组件的生命周期及其对应的钩子名称

挂载阶段：dom元素被渲染

- constructor(props) ：
  - 初始化 `props and state`；
  - 绑定事件处理函数
- componentWillMount
- componentDidMount

更新阶段：组件因为state改变被重新渲染

- componentWillReceiveProps
- shouldComponentUpdate
- componentWillUpdate
- componentDidUpdate

卸载阶段：组件从dom树中被删除

- componentWillUnmount



17版本前：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0fb3cd2923f04e4c8dc58243522ff666~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)



17版本后：https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/77843a0d241c4e8f9e70af2c8946f563~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)



- 新增两个：getDerivedStateFromProps、getSnapshotBeforeUpdate

### 11 PureComponent

类组件中被用来性能优化的组件，但是该组件官方已经不在推荐使用。

对props和state进行浅比较，如果props是对象且嵌套比较深的时候，可能不会比较，所以应该返回一个新的props而不是直接修改原props对象。

继承 `PureComponent` 的子类相当与定义了一个自定义的 [`shouldComponentUpdate`](https://zh-hans.react.dev/reference/react/Component#shouldcomponentupdate) 方法，该方法将浅比较 props 和 state。

memo：

- 使用 [`Object.is`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/is) 比较每个 prop，比较浅层是否想等。比如：`Object.is(3, 3)`为 `true`，但 `Object.is({}, {})` 为 `false`

https://zh-hans.react.dev/reference/react/PureComponent

```js
class Greeting extends PureComponent {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```

### 12 react和vue的异同

| 区别         | react                                                        | vue                                                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **核心思想** | 函数式编程，强调不可变数据与单向数据流，通过 `setState` 或 Hooks 驱动视图更新 | 渐进式框架，基于 MVVM 模式，默认支持响应式数据与双向绑定（`v-model`） |
| **模板语法** | 使用 JSX（JavaScript 扩展语法），HTML 与 JavaScript 混合编写 | 类 HTML 模板语法，通过指令（如 `v-if`、`v-for`）简化逻辑     |
| **状态管理** | 依赖外部库（如 Redux、MobX），需手动优化渲染（`React.memo`、`useCallback`） | 内置 Vuex（Vue 3 为 Pinia），响应式系统自动追踪依赖，精准更新组件 |
| **性能优化** | 虚拟 DOM + Diff 算法，需手动优化避免冗余渲染；Fiber 架构支持异步渲染 | 响应式系统自动追踪依赖，默认精准更新；编译时模板优化（如静态提升） |
| **生态系统** | 社区庞大，工具链丰富（Next.js、React Native），适合企业级应用与跨平台开发 | 中文文档友好，轻量易用；Nuxt.js 支持 SSR，适合快速迭代的中小型项目 |
| **学习曲线** | 陡峭（需熟悉 JSX、函数式编程及复杂状态管理）                 | 平缓（模板语法直观，文档详尽，适合新手快速上手）             |
| **适用场景** | 复杂交互应用、跨平台开发（如 React Native）                  | 中小型项目、快速原型开发                                     |
| **代码复用** | 通过高阶组件（HOC）或自定义 Hooks 实现逻辑复用               | 通过 Mixins 或 Composition API（类似 Hooks）实现逻辑复用     |
| **数据绑定** | 单向数据流，需手动实现双向绑定（如 `onChange` + `setState`） | 默认支持双向数据绑定（`v-model`），简化表单操作              |

共同点：

- 数据驱动视图

两者均通过数据变化自动更新视图，开发者无需直接操作 DOM，提升了开发效率

- 组件化开发

支持将 UI 拆分为可复用的组件，并通过 Props 实现父子通信，逻辑与视图解耦

- 虚拟 DOM 与 Diff 算法

通过虚拟 DOM 和高效的 Diff 算法优化渲染性能，减少真实 DOM 操作

### 13 手写useState、useDebounce、useDebounceFn、useDebounceEffect

参考ahooks的用法：https://ahooks.js.org/zh-CN/hooks/use-debounce-effect

```js
const useState = (defaultValue) => {
  const value = useRef(defaultValue);
  const setValue = (newValue) => {
    if (typeof newValue === "function") {
      // 如果传入的是函数，则直接调用该函数
      newValue(value.current);
    } else {
      value.current = newValue;
    }
  };
  return [value, setValue];
};

// 防抖
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  useEffect(() => {
    // 指定时间后的值发生变化
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  return debouncedValue;
}

const useDebounceFn = (fn, timeout) => {
  const timer = useRef();
  return (...arg) => {
    if (timer.current) clearTimeout(timer.current);
    timer.current = setTimeout(() => {
      fn(...arg);
    }, timeout);
  };
};

// 为useEffect增加防抖功能
const useDebounceEffect = (fn, deps, timeout) => {
  const timer = useRef();
  useEffect(() => {
    if (timer.current) clearTimeout(timer.current);
    timer.current = setTimeout(() => {
      fn();
    }, timeout);
    return () => clearTimeout(timer.current);
  }, deps);
};
```

### 14 useEffect和useLayoutEffect的区别

| 特点         | useEffect                                                    | useLayoutEffect                                              |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 渲染时机     | 组件渲染 - 浏览器绘制 - 执行useEffect回调                    | 组件渲染 - 执行useLayoutEffect回调 - 浏览器绘制              |
| 异步/同步    | 在浏览器完成 DOM 更新并绘制到屏幕后触发。不会阻塞浏览器渲染  | 在 DOM 更新后、浏览器绘制前立即触发。会阻塞浏览器渲染        |
| 事件循环机制 | `useEffect` 的回调被封装为**宏任务**，在下一轮事件循环中执行 | `useLayoutEffect` 的回调被封装为**微任务**，在 DOM 变更后同步执行 |
| 应用场景     | 数据请求、事件订阅；服务端渲染。如果在useEffect操作dom元素，可能会导致视觉闪烁 | DOM 测量、动态样式调整；动画、高频交互                       |

### 14 useCallback和useMemo

[面试官：useCallback，useMemo使用后性能一定好吗？](https://juejin.cn/post/7430644213888696371?share_token=B2AFDC22-EB4E-4B57-89F8-3C34FBA98B51)

对 useCallback、useMemo 这两个 hook 的理解，有什么样的区别，适合在什么场景下使用

- 是不是所有的变量或者函数都需要用这两个 hook 进行包裹
- 能不能量化一下，什么情况下需要使用
- 包裹后性能一定会好吗，为什么？
- 有没有更底层一点的理解

**useCallback（缓存函数本身）使用场景：**

- 将其作为 props 传递给包装在 [`memo`] 中的组件。如果 props 未更改，则希望跳过重新渲染。缓存允许组件仅在依赖项更改时重新渲染。
- 传递的函数可能作为某些 Hook 的依赖。比如，另一个包裹在 `useCallback` 中的函数依赖于它，或者依赖于 [`useEffect`](https://zh-hans.react.dev/reference/react/useEffect) 中的函数。

**包裹后性能一定会好吗:**

- 不一定，因为对函数引用存在缓存，缓存也是需要花时间的；
- 如果频繁触发更新，缓存经常失效，反而会增加react为维护和清理缓存所付出的性能开销。

**底层原理：**

- react更新机制，父组件的props改变会触发子组件重新渲染，比如函数/对象的引用地址（会在内存中创建新的实例）发生变化，也会被重新渲染。
- useMemo和useCallback会比较依赖性是否改变，不改变就返回上一次的引用地址。

### 15 React 组件中绑定一个事件跟直接操作 DOM 绑定一个事件有什么差别

| **对比维度**     | **React 事件绑定**                                           | **原生 DOM 事件绑定**                                  |      |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------ | ---- |
| **事件对象**     | 使用 `SyntheticEvent`，跨浏览器封装，提供统一接口，事件对象会被回收，不可异步访问 | 直接使用浏览器原生 `Event` 对象，行为依赖浏览器实现    |      |
| **事件绑定方式** | 通过 JSX 属性绑定（如 `onClick={handler}`），由 React 内部统一委托到根容器（事件冒泡） | 使用 `addEventListener` 直接绑定到具体元素             |      |
| **事件传播机制** | 在虚拟 DOM 层模拟捕获/冒泡，实际通过根容器代理捕获事件并分发 | 依赖原生 DOM 树的事件捕获/冒泡流程                     |      |
| **事件解绑**     | 自动管理，组件卸载时自动移除事件监听                         | 需手动调用 `removeEventListener`，否则可能导致内存泄漏 |      |
| **性能优化**     | 通过事件池复用 `SyntheticEvent`，减少内存开销；默认事件委托减少 DOM 绑定数量 | 高频事件需手动优化（如防抖、节流或手动事件委托）       |      |
| **异步事件访问** | 异步代码中需调用 `e.persist()` 保留事件对象                  | 原生事件对象始终可用，可直接异步操作                   |      |

### 16 合成事件

**事件冒泡的应用**。

React 合成事件（SyntheticEvent）是 React 对浏览器原生事件的跨浏览器封装，旨在提供统一的 API 和跨平台一致性。其核心目标包括：

- **跨浏览器兼容性**：通过封装原生事件，消除不同浏览器（如 IE 与现代浏览器）的事件行为差异

- **性能优化**：通过事件委托（Event Delegation）减少事件监听器数量，并通过事件池（Event Pooling）复用事件对象，降低内存开销

- **简化开发**：开发者无需手动管理事件绑定/解绑，避免内存泄漏风险

事件池：React 复用合成事件对象以减少内存分配。例如，连续点击按钮时，同一事件对象会被重置并重复使用。

```js
const handleClick = (event) => {
  event.persist(); // 阻止事件对象回收
  setTimeout(() => {
    console.log(event.target); // 异步操作需手动保留事件对象
  }, 1000);
};
```

### 17 state层级太深，比如a.b.c.d，怎更新d的数据

使用useImmer只修改需要更改的属性。

```js
import { useImmer } from 'use-immer'; // here

  const [person, updatePerson] = useImmer({ // here
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e) {
    updatePerson(draft => {
      draft.name = e.target.value; // here
    });
  }
```

同样的useImmerReducer也可以替换掉useReducer。

```js
import { useImmerReducer } from 'use-immer';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

function tasksReducer(draft, action) {
  switch (action.type) {
    case 'added': {
      draft.push({
        id: action.id,
        text: action.text,
        done: false,
      });
      break;
    }
    // case 'changed': {
    //   return tasks.map((t) => {
    //     if (t.id === action.task.id) {
    //        return action.task;
    //     } else {
    //       return t;
    //     }
    //    });
    // }
    // 修改后的写法，直接使用arr[i]方式访问
    case 'changed': {
      const index = draft.findIndex((t) => t.id === action.task.id);
      draft[index] = action.task;
      break;
    }
    case 'deleted': {
      return draft.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('未知 action：' + action.type);
    }
  }
}

export default function TaskApp() {
  const [tasks, dispatch] = useImmerReducer(tasksReducer, initialTasks);

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>布拉格的行程安排</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: '参观卡夫卡博物馆', done: true},
  {id: 1, text: '看木偶戏', done: false},
  {id: 2, text: '打卡列侬墙', done: false},
];

```

### 18 redux和mobx的异同

相同点：都是用于状态管理

不同点：https://juejin.cn/post/7087338460129787941、https://juejin.cn/post/7080795642217365541

> 存储的数据结构:
>
> 1. Redux默认是存储的一个原生的JavaScript对象（所以每次都要返回一个新的state，不能再原来的state上面更新），而MobX则是存贮了一个可观察的对象(proxy)
> 2. Redux需要手动追踪所有状态对象的变更
> 3. MobX中可以监听可观察对象，当其变更时讲自动触发监听（感觉炸一看跟Vue的双向绑定挺像，但是细看其实MobX还是需要组件触发事件，随后再去更改数据）

