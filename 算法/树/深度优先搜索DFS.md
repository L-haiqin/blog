深度优先搜索(Depth First Search)(其实是**二叉树的先序遍历**)是沿着树的深度遍历树的节点，尽可能深的搜索树的分支。

```javascript
var levelOrder = function(root) {
    if(!root) return [];
    let result = [];  // 存放结果
   
    // 深度优先搜索(Depth First Search)(其实是**二叉树的先序遍历**)是沿着树的深度遍历树的节点，尽可能深的搜索树的分支。
    dfs(root,0,result);
    return result;
};

function dfs(root,step,result){
    if(root){
        if(!result[step]) result[step] = [];  // 此判断必须这样写，result[step] ==== null 无效
        result[step].push(root.val);  // 用来记录每一层的节点
        dfs(root.left, step + 1, result);
        dfs(root.right, step + 1, result);
    } 
}
```

