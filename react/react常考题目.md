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

### 11 react为什么使用fiber

一种协调调度算法。fiber是一个js对象，代表组件或dom元素的虚拟节点。

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