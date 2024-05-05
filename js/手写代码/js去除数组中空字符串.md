#### js去除数组中空字符串

1、for循环遍历，用`splice(i,1)`删除为空的元素

2、filter：` let newArr=arr.filter(i=>i && i.trim()) `，可以过滤掉null、undefined、''和'    '。

例子：

```javascript
let arr = ['c', '', 'g', null, undefined, 'f', '  '];
let newArr=arr.filter(i=>i && i.trim())
console.log(newArr); // [ 'c', 'g', 'f' ]
```

