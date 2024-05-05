#### node奇葩问题

#### 1、process.argv

`process` 对象是一个全局变量（无需使用 `require()`），它提供当前 Node.js 进程的有关信息，以及控制当前 Node.js 进程。

`process.argv` 属性返回一个数组，这个数组包含了启动Node.js进程时的命令行参数：

第一个元素为[`process.execPath`](http://nodejs.cn/s/MCrAya)， 返回启动 Node.js 进程的可执行文件的绝对路径名 。如果需要获取`argv[0]`的值请参见node文档的 `process.argv0`。

第二个元素为当前执行的JavaScript文件路径。

剩余的元素为其他命令行参数。

例如，`process-args.js`文件有以下代码:

```javascript
// print process.argv
 
process.argv.forEach((val, index) => {
 
console.log(`${index}: ${val}`);
 
});
```

运行以下命令，启动进程：

```
$ node process-args.js one two=three four
```

将输出：

```
0: /usr/local/bin/node
 
1: /Users/mjr/work/node/process-args.js
 
2: one
 
3: two=three
 
4: four
```

