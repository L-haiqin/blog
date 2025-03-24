| 维度           | webpack                                       | vite                                                         | rollup                                           |
| -------------- | --------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| **打包原理**   | 预分析依赖关系，**全量打包**成 bundle         | 开发时**按需编译**（按需加载） esbuild 模块，生产环境用 Rollup 打包 | 静态分析依赖，生成扁平化代码                     |
| **模块处理**   | 支持 CommonJS、AMD、ESM 等多种模块            | 专注ES模版                                                   | 专注ES模版                                       |
| **构建速度**   | 冷启动需全量分析依赖并打包，项目越大速度越慢  | 利用浏览器原生 ESM 按需编译，冷启动秒级完成                  | 生产构建高效，但开发模式无内置服务器，需手动配置 |
| **配置复杂度** | 高度灵活但配置复杂（Loader、Plugin 体系庞大） | 开箱即用，预设 React/Vue 等模板，配置简洁                    | 配置相对简单，但需插件支持 CSS 等非 JS 资源      |
| **应用场景**   | 大型企业级应用，传统项目或兼容旧浏览器        | 现代框架项目（Vue/React）                                    | js库或者sdk                                      |

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bf2c472e44714dafa5bebcd03822e492~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)



![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1c02296864e54069b798356f172fc34b~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)