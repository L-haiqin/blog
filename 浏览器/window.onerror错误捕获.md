> [错误监控](https://juejin.cn/post/6972437230178533413)

### try catch

- 只能捕获同步任务，不能捕获异步任务

```js
  try{
    setTimeout(()=>{
      console.log(window.a.b)
    },2000)
  }catch{
    console.log(e)
  }
  // Uncaught TypeError: Cannot read property 'b' of undefined

```

- 不能捕获语法错误

```js
  try{
    let name = 'hello world;
    console.log(name)
  }catch{
    console.log(e)
  }
  // Uncaught SyntaxError: Invalid or unexpected token
```

### window.onerror

- 全局捕获方式，可以捕获同步和异步错误
- 不能捕获网络异常错误
- 捕获到的信息比较丰富，比如行信息、堆栈信息等

```js
throw new Error('啦啦啦=====')
window.onerror = function(message, source, lineno, colno, error) { 
  函数参数：
  *   message：错误信息（字符串）。可用于HTML onerror=""处理程序中的event。
  *   source：发生错误的脚本URL（字符串）
  *   lineno：发生错误的行号（数字）
  *   colno：发生错误的列号（数字）
  *   error：Error对象
  *   若该函数返回true，则阻止执行默认事件处理函数，如异常信息不会在console中打印。没有返回值或者返回值为false的时候，异常信息会在console中打印
  console.log('message',message)
  console.log('source', source)
  console.log('lineno',lineno)
  console.log('colno',colno)
  console.log('error', error)
 }
// message Uncaught Error: 啦啦啦=====
// source http://localhost:3000/static/js/vendors~main.chunk.js
// lineno 25431
// colno 9
// error Error: 啦啦啦=====
    at App (App.js:5)
    at renderWithHooks (react-dom.development.js:14985)
    at mountIndeterminateComponent (react-dom.development.js:17811)
    at beginWork (react-dom.development.js:19049)
    at HTMLUnknownElement.callCallback (react-dom.development.js:3945)
    at Object.invokeGuardedCallbackDev (react-dom.development.js:3994)
    at invokeGuardedCallback (react-dom.development.js:4056)
...

```

### window.addEventListener("error")

- 跟上面的onerror差不多，能捕获同步、异步代码和网络异常错误
- 第二个参数为true-事件捕获，false-事件冒泡

```js
window.addEventListener('error', function(event) { ... })

当资源（如img或script）加载失败，加载资源的元素会触发一个Event接口的error事件，并执行该元素上的onerror()处理函数。这些error事件不会向上冒泡到window，但可以在捕获阶段被捕获
因此如果要全局监听资源加载错误，需要在捕获阶段捕获事件
//图片加载失败使用默认图片，依旧加载失败超过三次使用base64图片
window.addEventListener('error',function(e){
    let target = e.target, // 当前dom节点
        tagName = target.tagName,
        count = Number(target.dataset.count ) || 0, // 以失败的次数，默认为0
        max= 3; // 总失败次数，此时设定为3
    // 当前异常是由图片加载异常引起的
    if( tagName.toUpperCase() === 'IMG' ){
        if(count >= max){
            target.src = 'data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD//AK3/ALYH+5hX6FV5N4Y/5GHwx/vyf+iJa9ZrysPhoYVShDZu/potDmwWFhhIzhT2bv6aLQ//Z';
        }else{
            target.dataset.count = count + 1;
            target.src = '//xxx/default.jpg';
        }
    }
},true)
```

### window.addEventListener('unhandledrejection')

- 捕获异步错误，没有catch的promise错误

```js
//捕获到的错误信息在err.reason上
new Promise((resolve,reject)=>{
    setTimeout(()=>{
        reject('not found')
    })
})
window.addEventListener('unhandledrejection',function (err: any) {
    console.log(err.reason)
})
// Uncaught (in promise) not found
```

### react的错误处理

react的ErrorBoundary 错误边界是一种 React 组件，这种组件可以捕获发生在其子组件树任何位置的 JavaScript 错误，并打印这些错误，同时展示降级 UI，而并不会渲染那些发生崩溃的子组件树。错误边界在渲染期间、生命周期方法和整个组件树的构造函数中捕获错误。

但不能捕获异步错误。

使用：类组件里面定义getDerivedStateFromError和componentDidCatch，就会被变成一个错误边界。

```js
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染能够显示降级后的 UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 你同样可以将错误日志上报给服务器
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 你可以自定义降级后的 UI 并渲染
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

或者可以使用react-error-boundary库。

```js
import { ErrorBoundary } from "react-error-boundary";

function Bbb() {
  const b = window.a.b;

  return <div>{b}</div>
}

function fallbackRender({ error }) {
  return (
    <div>
      <p>出错了：</p>
      <div>{error.message}</div>
    </div>
  );
}

export default function App() {
  return <ErrorBoundary fallback={fallbackRender}>
    <Bbb></Bbb>
  </Erro
```

