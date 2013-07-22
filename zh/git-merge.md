-----------------------
git-merge 手册页
===============

-----------------------
####名字
`git-merge` - 合并两个或更多的提交历史

####语法
```
    git merge [-n][--stat][--no-commit][--squash][--[no-]edit]
			[-s <strategy>][-X <strategy-option>]
	git merge <msg> HEAD <commit> ...
	git merge --abort
```

####描述
把指定提交的改动记录（指上次从当前分支分离后发生的所有改动）并入当前分支。这个命令被`git pull`用来合并另外一个版本库的改动，也可以被用来手工合并一个分支的改动到另外一个分支。

假设存在以下历史提交记录并且当前所在分支是`master`
```
		  A---B---C topic
		 /
	D---E---F---G master
```
执行`git merge topic`将把`topic`分支自从`master`分离（例如：`E`）直到当前最新提交（例如：`C`）的所有改动重放到`master`，然后生成新的提交并记录它的两个父提交的名字和用户描述的日志信息。
```
		  A---B---C topic
		 /         \
	D---E---F---G---H master
```

第二种语法（`<msg> HEAD <commit> ...`）因为历史原因而存在。不要在命令行或者新的脚本代码中使用它。它等价于`git merge -m <msg> <commit> ....`

第三种语法（`git merge --abort`）只能在合并产生冲突之后使用。它会终止合并，并且恢复暂存区和目录文件到合并之前的状态。然而，当合并开始时有未提交的改动（特别是那些合并过程中被进一步修改的改动），`git merge --abort`将在某种程度上无法恢复到合并之前的状态。基于这一点： <br>
**警告：**不推荐在包含未提交改动的版本库中执行`git merge`，这样做有可能让你在合并产生冲突后无法回退。

####选项
`--commit`
`--no-commit`
:		aafsad

`--edit`
`--no-edit`
:		hello

`--ff`
`--no-ff`
`--ff-only`

`--log[=<n>]`
`--no-log`

`--stat`
`-n`
`--no-stat`

`-squash`
`--no-squash`

`-s <strategy>`
`--strategy=<strategy>`

`-X <option>`
`--strategy-option=<option>`

`--summary`
`--no-summary`

`-q`
`--quiet`

`-v`
`--verbose`
`--progress`
`--no-progress`

`-m <msg>`

`--rerere-autoupdate`
`--no-rerere-autoupdate`

`--abort`

`<commit>...`



####预合并
在合并别人的提交之前，你应该整理好你手头上的工作并且提交到本地仓库，这样不至于在合并冲突时丢失一些文件改动。参见`git-stash`。如果本地有未提交的改动并且这些改动和将要被`git pull/git merge`更新的改动有重合，`git pull`和`git merge`指令就会停止执行。

为了避免在合并时记录下无关的改动，如果暂存区中有任何相对于版本库的改动，`git pull`和`git merge`将会中止执行。（有一种例外情况是这样的：暂存区中改动的文件和合并生成的文件是完全一样的。）

如果所有指定的提交已然是`HEAD`的祖先，`git merge`将会给出提示“Already up-to-date”并退出。

####快进式合并
当前分支头常常是指定提交的祖先。这是最普遍的场景特别是被`git pull`触发的时候：你在追踪上游版本库，没有本地提交，现在你想更新到最新的上游版本。这种情况下，没必要生成一个新的提交以记录这次合并的历史。取而代之的是，`HEAD`指针被修改指向到指定提交（同时更新的还有暂存区），并不生成额外的合并提交记录。

这种快进式行为可以被这个参数选项禁用：`--no-ff`。

####真实合并

####冲突是怎么呈现的

####怎么解决冲突

####示例

####合并策略

####配置项

####参考

####GIT
`git`指令的一部分。

> Written with [StackEdit](http://benweet.github.io/stackedit/).