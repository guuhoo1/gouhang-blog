# git 日常常用命令

> 克隆仓库

`git clone https://github.com/xxxx/xxxx.git`

> 关联仓库

`git remote add origin https://github.com/xxxx/xxxx.git`

> 取消关联仓库
`git remote remove origin`

> 提交

```
git add . // 添加缓存区域
git commit -m "xxx" // 提交信息
git push origin master // 提交到远程仓库
```

> 拉取远程仓库

`git pull origin master`

> 创建分支

`git branch xxx`

> 切换分支

`git checkout xxx`

> 删除分支

`git branch -d xxx`

> 创建远程分支

`git push origin xxx`

> 删除远程分支

`git push origin --delete xxx`

> 删除本地分支

`git branch -d xxx`

> 创建远程仓库

`git remote add origin https://github.com/xxx/xxx.git`

> 删除远程仓库

`git remote rm origin`

> 合并分支

`git merge xxx`

> 创建标签

`git tag xxx`

> 更新同步分支列表

`git fetch`

> 暂存到缓冲区

`git stash`

> 从缓冲区取出

`git stash pop`

```js
// 当你正在做某件事的时候，你的老板来了
// ，要求你立即修复某件事。
// 传统上，你会向一个临时分支提交，以储存你的修改，然后返回到你的原始分支进行紧急修复，就像这样：

// 可以通过 你可以用’git stash’来简化上述工作，像这样：
# ... 嗨骇害 ...
$ git stash // 暂存当前修改
$ edit emergency fix // 处理紧急问题
$ git commit -a -m "紧急修复" // 提交代码
$ git stash pop // 把缓存的东西取出来继续编写未编写完的代码
# ... 继续骇入 ...
```

> 设置 git push 默认行为

`git push --set-upstream origin master`

- 推送本地更改： 这个命令首先会将你本地 master 分支上的所有提交（自上次推送以来所做的更改）推送到名为 origin 的远程仓库中相应的 master 分支。

- 设置上游（Upstream）分支： --set-upstream 选项告诉 Git，本地的 master 分支应该跟踪（或说是“关联”）远程仓库 origin 中的 master 分支。这样设置之后，未来的 git pull 和 git push 操作将默认使用这个远程分支，你无需每次都指定远程仓库和分支名称

- 简化后续操作： 一旦上游分支被设置，你就可以简单地使用 git push 或 git pull，Git 会自动知道要推送或拉取到哪个远程分支。这大大简化了工作流程，特别是当你经常与同一个远程分支交互时
