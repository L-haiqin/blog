#### Git工作流程

一般工作流程如下：

- 从远程仓库中克隆 Git 资源作为本地仓库；

- 从本地仓库中checkout代码然后进行代码修改；

- 在提交本地仓库前先将代码提交到暂存区；

- 提交修改，提交到本地仓库；本地仓库中保存修改的各个历史版本；

- 在需要和团队成员共享代码时，可以将修改代码push到远程仓库。

工作流程图如下：

 ![31563bec07f32cdf2c44d3882c570573.png](https://img-blog.csdnimg.cn/img_convert/31563bec07f32cdf2c44d3882c570573.png)











#### Git的几个核心概念

**工作区、暂存区、版本库、远程仓库**

Git和其他版本控制系统如SVN的一个不同之处就是有暂存区的概念。

- Workspace： 工作区，就是你平时存放项目代码的地方

- Index / Stage： 暂存区，用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息

- Repository： 仓库区（或版本库），就是安全存放数据的位置，这里面有你提交到所有版本的数据。其中HEAD指向最新放入仓库的版本

- Remote： 远程仓库，托管代码的服务器，可以简单的认为是你项目组中的一台电脑用于远程数据交换

#### 分支

每次的提交Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里这个分支叫**主分支，即master分支**。HEAD指针严格来说不是指向提交，而是指向master，master才是指向提交的。

一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点：

 ![a5a875d94fde9de79d0eb7a495a38574.png](https://img-blog.csdnimg.cn/img_convert/a5a875d94fde9de79d0eb7a495a38574.png) 


每次提交，master分支都会向前移动一步，这样随着不断提交，master分支的线也越来越长。

当我们**创建新的分支**，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上：

 ![cc341cbb67c83072cc5f3261cbefaf71.png](https://img-blog.csdnimg.cn/img_convert/cc341cbb67c83072cc5f3261cbefaf71.png) 


Git创建一个分支很快，因为除了增加一个dev指针，改改HEAD的指向，工作区的文件都没有任何变化！

不过切换到了dev分，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变：

 ![55815b0dd00387e349b14d10d2989381.png](https://img-blog.csdnimg.cn/img_convert/55815b0dd00387e349b14d10d2989381.png) 

假如我们在dev上的工作完成了，就可以把dev**合并**到master上。Git怎么合并呢？最简单的方法，就是直接把master指向dev的当前提交，就完成了合并：

 ![d5436f68b7c79c69972326ec3b361fbe.png](https://img-blog.csdnimg.cn/img_convert/d5436f68b7c79c69972326ec3b361fbe.png) 


所以Git合并分支也很快！就改改指针，工作区内容也不变！

合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后就剩下了一条master分支：

 ![15c5ca8ce1dd9c9aada0637fd4578541.png](https://img-blog.csdnimg.cn/img_convert/15c5ca8ce1dd9c9aada0637fd4578541.png) 

#### 与分支有关的命令

- 查看当前分支：`git branch`  （ 会列出所有分支，当前分支前面会标一个`*`号）

- 查看项目的分支们(包括本地和远程) ：`git branch -a`

- **创建分支**：`git branch <name> `   （创建的好像是本地分支，而不是远程分支？？？）

`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

```ruby
// 方式一
$ git checkout -b dev
Switched to a new branch 'dev'

// 方式二
$ git branch dev
$ git checkout dev
```

- **切换分支**：`git checkout <name>`

- 创建+切换分支：`git checkout -b <name> `

- **合并某分支到当前分支**：`git merge <name> `

- **删除本地分支**：`git branch -d <name>  `

- 删除远程分支：`git push origin --delete <name> `

- 删除远程分支`dev`：`git push origin :dev` ，`origin`和`:dev`之间有空格

- 推送到远程分支：`git push origin master`

- 回退命令 ：

```ruby
$ git reset --hard HEAD^         回退到上个版本
$ git reset --hard HEAD~3        回退到前3次提交之前，以此类推，回退到n次提交之前
$ git reset --hard commit_id     退到/进到 指定commit的sha码

$ git reset –hard 8ff24a6803173208f3e606e32dfcf82db9ac84d8    回滚到之前某一commit
```

**出现push不了的问题：**

```ruby
1.使用强制push的方法：

$ git push -u origin master -f 

这样会使远程修改丢失，一般是不可取的，尤其是多人协作开发的时候。

2.push前先将远程repository修改pull下来

$ git pull origin master

$ git push -u origin master

3.若不想merge远程和本地修改，可以先创建新的分支：

$ git branch [name]

然后push

$ git push -u origin [name]
```

**因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。** 



 ![31563bec07f32cdf2c44d3882c570573.png](https://img-blog.csdnimg.cn/img_convert/31563bec07f32cdf2c44d3882c570573.png)

参考博客：[看完这篇还不会用Git，那我就哭了！](https://zhuanlan.zhihu.com/p/94008510)、[工作流一目了然，看小姐姐用动图展示10大Git命令](https://zhuanlan.zhihu.com/p/132573100)

补充：

```text
# 更改你的最新提交消息
git commit --amend 'new commit message' 

# 将一系列提交合并为一个提交，你可能会用它来组织混乱的提交历史记录
git rebase -i

# 合并分支：
# 1.首先确保你正在查看 master 分支
git checkout master

# 2.现在将你的分支合并到 master 
git merge prc/dev-wupx

# 显示存储库中所有提交的列表 该命令显示有关提交的所有信息，例如提交ID，作者，日期和提交消息
git log

# 显示带有作者姓名的文件的更改历史记录
git blame 'filename'
```

 ![img](https://pic2.zhimg.com/80/v2-31a3f3d822e9c7cd2264054b0808ab61_720w.jpg) 