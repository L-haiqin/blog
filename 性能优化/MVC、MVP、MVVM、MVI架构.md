### 1 区别

| **架构** |                      **核心角色与职责**                      |                        **数据流向**                         |                  **耦合性**                   |                       **适用场景**                       |
| :------: | :----------------------------------------------------------: | :---------------------------------------------------------: | :-------------------------------------------: | :------------------------------------------------------: |
| **MVC**  | **Model**（数据与业务逻辑）、**View**（界面展示）、**Controller**（协调Model与View） |             用户操作→Controller→Model→View更新              |  高（View与Model直接交互，Controller易臃肿）  |               小型Web应用（如传统PHP项目）               |
| **MVP**  | **Model**（数据）、**View**（界面与用户输入）、**Presenter**（逻辑处理与数据转换） |        用户操作→View→Presenter→Model→Presenter→View         | 中（通过接口解耦，但Presenter需持有View引用） |    桌面应用、早期Android开发（需严格分离界面与逻辑）     |
| **MVVM** | **Model**（数据）、**View**（界面）、**ViewModel**（数据绑定与状态管理） |   用户操作→View→ViewModel→Model→自动更新View（双向绑定）    |   低（数据驱动UI，ViewModel不直接操作View）   | 现代Web应用（如Vue、React+状态管理）、数据交互复杂的场景 |
| **MVI**  | **Model**（状态）、**View**（界面）、**Intent**（用户意图）、**ViewModel**（状态处理） | 用户操作→Intent→ViewModel→生成新状态→View更新（单向数据流） |        极低（状态不可变，数据流唯一）         | 高复杂度应用（如金融系统、实时协作工具），需严格状态管理 |

- 数据流方向：MVC/MVP为双向，MVVM为双向绑定，MVI为单向
- 状态管理：MVI强制不可变状态，所有变更通过新状态对象实现，而MVVM允许直接修改数据
- 测试性：MVP和MVI因接口/单向流更易测试，MVC因Controller耦合度高而难以测试



![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0fe2562a640c4a3f8364648c720c18a2~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=947&h=479&s=120010&e=png&b=fefefe)



![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d396c45172fa4443a47df53c2f896f2a~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=765&h=469&s=102408&e=png&b=fefdfd)



![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cd864035558843b5958dcfe5c235d953~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=647&h=445&s=58509&e=png&b=fdfbfb)



### 2 各架构在React中的适用场景

**MVC**：

- 适用性低，React组件本身融合了View和Controller（如useState管理状态，事件处理函数充当Controller）

**MVP**：

- 可通过接口分离实现（如将业务逻辑抽离到Presenter类），但React社区更倾向MVVM

**MVVM**：

- 最佳实践：使用useState + useEffect实现数据驱动，或结合Context API全局状态管理
- 示例：登录表单的数据绑定与自动验证。

**MVI**：

- 复杂场景：需严格状态追踪（如多步骤表单、实时聊天），通过useReducer或Redux实现单向流
- 优势：状态变更可预测，易于调试（如Redux DevTools追踪每个Action）。

### 3 例子

MVVM：（通过Hooks）

```js
// Login.jsx（View层）
import React, { useState } from 'react';
import { useLoginViewModel } from './LoginViewModel';

const Login = () => {
  const { username, setUsername, password, setPassword, handleLogin } = useLoginViewModel();
  
  return (
    <div>
      <input value={username} onChange={(e) => setUsername(e.target.value)} />
      <input type="password" value={password} onChange={(e) => setPassword(e.target.value)} />
      <button onClick={handleLogin}>登录</button>
    </div>
  );
};

// LoginViewModel.js（ViewModel层，MVVM核心）
export const useLoginViewModel = () => {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');

  const handleLogin = async () => {
    // 模拟API调用（Model层）
    const response = await fetch('/api/login', { 
      method: 'POST',
      body: JSON.stringify({ username, password })
    });
    if (response.ok) {
      // 数据驱动UI更新（MVVM特性）
      window.location.href = '/dashboard';
    }
  };

  return { username, setUsername, password, setPassword, handleLogin };
};
```

MVI:（通过单向数据流）

```js
// 定义状态和意图（MVI核心）
const initialState = { username: '', password: '', isLoading: false };

const loginReducer = (state, action) => {
  switch (action.type) {
    case 'SET_USERNAME':
      return { ...state, username: action.payload };
    case 'SET_PASSWORD':
      return { ...state, password: action.payload };
    case 'LOGIN_START':
      return { ...state, isLoading: true };
    case 'LOGIN_SUCCESS':
      return { ...initialState };
    default:
      return state;
  }
};

// 在组件中使用useReducer实现MVI
const LoginMVI = () => {
  const [state, dispatch] = useReducer(loginReducer, initialState);
  
  const handleLogin = async () => {
    dispatch({ type: 'LOGIN_START' });
    const res = await api.login(state);
    if (res.success) {
      dispatch({ type: 'LOGIN_SUCCESS' });
    }
  };

  return (
    /* 类似MVVM的UI结构，但状态通过dispatch更新 */
  );
};
```

MVC:

```js
// models/UserModel.js
export class UserModel {
  static async login(credentials) {
    const response = await fetch('/api/login', {
      method: 'POST',
      body: JSON.stringify(credentials)
    });
    return response.json(); // 返回 { success: boolean, user?: object }
  }

  static saveUser(user) {
    localStorage.setItem('currentUser', JSON.stringify(user));
  }
}

// controllers/AuthController.js
import { UserModel } from '../models/UserModel';
import { history } from '../utils/history';

export class AuthController {
  static handleLogin = async (values) => {
    try {
      const result = await UserModel.login(values);
      if (result.success) {
        UserModel.saveUser(result.user); // 持久化用户数据
        history.push('/dashboard'); // 路由跳转
      } else {
        alert('登录失败: ' + result.message);
      }
    } catch (error) {
      alert('网络错误: ' + error.message);
    }
  };
}

// views/LoginForm.jsx
import { Form, Input, Button } from 'antd';

const LoginForm = ({ onLogin }) => {
  const [form] = Form.useForm();

  return (
    <Form form={form} onFinish={onLogin}>
      <Form.Item name="username" rules={[{ required: true }]}>
        <Input placeholder="用户名" />
      </Form.Item>
      <Form.Item name="password" rules={[{ required: true }]}>
        <Input.Password placeholder="密码" />
      </Form.Item>
      <Button type="primary" htmlType="submit">登录</Button>
    </Form>
  );
};

// components/LoginPage.jsx
import { useState } from 'react';
import { AuthController } from '../controllers/AuthController';
import LoginForm from '../views/LoginForm';

const LoginPage = () => {
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (values) => {
    setLoading(true);
    await AuthController.handleLogin(values);
    setLoading(false);
  };

  return (
    <div className="login-container">
      <LoginForm onLogin={handleSubmit} />
      {loading && <div>登录中...</div>}
    </div>
  );
};
```

