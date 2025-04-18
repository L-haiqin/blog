> 面试题：
>
> - js bridge通信原理
>
> - 怎么实现js bridge跨多个app共用
>
> - 明明是不同的语言，为什么js和native可以通信
>   - 类似于js和java怎么通信呢，一般是发送axios请求

## 1、js和native的通信

### 传统桥接机制（Bridge）

- **步骤1：JS调用Native**
  JS通过`NativeModules`调用Native模块方法，请求被序列化为JSON消息，通过桥接队列发送到Native线程。

  示例：`NativeModules.CameraModule.openCamera()`调用后，消息通过`MessageQueue`传递到Native层

- **步骤2：Native处理并返回**
  Native模块（如Java的`@ReactMethod`或OC的`RCT_EXPORT_METHOD`）处理请求，通过回调（Callback/Promise）或事件（`RCTDeviceEventEmitter`）返回结果.

  示例：Android的`@ReactMethod`方法处理完成后，通过`Promise.resolve(data)`返回数据

### JSI（JavaScript Interface）

- **步骤1：Native模块绑定**

  使用`TurboModule`规范定义接口，通过Codegen工具生成C++绑定代码

- **步骤2：JS直接调用**

  JS通过JSI接口直接调用Native方法，例如`NativeModules.Camera.takePhoto()`直接触发Native相机

## 2、js bridge的通信原理

[JSBridge原理 - 前端H5与客户端Native交互](https://juejin.cn/post/7355117271213899776)

### 概念

JSBridge 充当了 Web 应用和原生应用之间的通信桥梁。通过 JSBridge，我们可以在 web 和原生代码之间进行双向通信，比如使用原生应用的某些功能（相机、地图、设备信息等）。

### h5 > native

**方法一：拦截url schema**

针对不同的url，进行不同的处理。

![image.png-62.8kB](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/21feb64287fe4db1987329a6ab8ac3af~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

比如在手机safri里面直接输入weixin://，就会提示我们打开微信。tao bao://也是一样的。同样也支持打开电话号码tel:15696741615。

有点类似于[Linking](https://www.react-native.cn/docs/next/linking)里面对schema url的使用。

Linking.canOpenURL

Linking.openURL

![image.png-153.6kB](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d88f17ac906e4b32b80478dfd7c3e311~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

**方法二：向Webview中注入JS API**

App将Native的相关接口注入到JS的Context（window）的对象中，一般来说这个对象内的方法名与Native相关方法名是相同的，Web端就可以直接在全局**window**下使用这个暴露的全局JS对象，进而调用原生端的方法。

### native > h5

类似于eval的方法，native直接提供一个可以执行js代码的编辑器。



