### 1 传统桥接机制（Bridge）

- **步骤1：JS调用Native**
  JS通过`NativeModules`调用Native模块方法，请求被序列化为JSON消息，通过桥接队列发送到Native线程。

  示例：`NativeModules.CameraModule.openCamera()`调用后，消息通过`MessageQueue`传递到Native层

- **步骤2：Native处理并返回**
  Native模块（如Java的`@ReactMethod`或OC的`RCT_EXPORT_METHOD`）处理请求，通过回调（Callback/Promise）或事件（`RCTDeviceEventEmitter`）返回结果.

  示例：Android的`@ReactMethod`方法处理完成后，通过`Promise.resolve(data)`返回数据

### 2 JSI（JavaScript Interface）

- **步骤1：Native模块绑定**

  使用`TurboModule`规范定义接口，通过Codegen工具生成C++绑定代码

- **步骤2：JS直接调用**

  JS通过JSI接口直接调用Native方法，例如`NativeModules.Camera.takePhoto()`直接触发Native相机

