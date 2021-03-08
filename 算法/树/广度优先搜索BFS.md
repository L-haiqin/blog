广度优先搜索(Breadth First Search)(其实是**二叉树的层次遍历**)，又叫宽度优先搜索或横向优先搜索，是从根结点开始沿着树的宽度搜索遍历。

```javascript
var levelOrder = function(root) {
    if(!root) return [];
    let queue = [root];  // 队列，记录当前层的结点
    let result = [];
    let step = 0;

    // 广度优先搜索(Breadth First Search)(其实是二叉树的层次遍历),又叫宽度优先搜索或横向优先搜索，是从根结点开始沿着树的宽度搜索遍历
    while(queue.length){
        result[step] = [];
        let len = queue.length;

        while(len){
            // shift()用于把数组的第一个元素从其中删除，并返回被删除的值。如果数组是空的，shift()方法将不进行任何操作，返回undefined的值
            let first = queue.shift();
            result[step].push(first.val);
            // console.log(first)
            if(first.left) queue.push(first.left);
            if(first.right) queue.push(first.right);
            len--;
        }
        step++;
        // console.log(queue)
    }
    return result;
};
```
