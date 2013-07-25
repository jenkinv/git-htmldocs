-----------------------
git-merge 手册页
===============

-----------------------

####名字
`git-merge` - 合并两个或多个提交历史

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
>执行合并，然后提交结果。`--commit`可以用来覆盖`--no-commit`。  
>`--no-commit`执行合并，但假设合并失败，不做自动合并，给用户合并前继续修改的机会。

`--edit`
`--no-edit`  
>在提交自动合并之前打开一个编辑器，让你有机会编辑已经自动生成的日志，比如解释合并的理由。`--no-edit`选项可以被用来接受自动生成的日志（通常不建议这么干）。如果你之前在命令行通过`-m`选项已经提供了一条草稿日志，`--edit`依然有用，可以让你进一步编辑。  
>老的脚本由于历史原因可能会不允许用户编辑合并日志。用户运行`git merge`然后看不到编辑器被打开(译者注：原文可能笔误，说可以打开编辑器，这样的话应该就和上面一致了就不需要纠正了)。为了纠正这种行为，可以设置环境变量`GIT_MERGE_AUTOEDIT`为`no`。

`--ff`
>当合并被认定为可以进行快进，仅仅更新分支的指针，而不再创建一次合并提交。这是默认行为。

`--no-ff`
>当合并被认定为可以进行快进，仍然创建一次合并提交。

`--ff-only`
>只允许快进式合并或者已经合并完成，否则将会拒绝合并以失败的结果退出。

`--log[=<n>]`
`--no-log`
>计算日志时，除了那些分支名字以外，还包括来自实际合并的最多<n>次提交的单行描述。参见`git-fmt-merge-msg(1)`  
`--no-log`不会列出来自实际合并的提交的单行描述。

`--stat`  
`-n`  
`--no-stat`
>在合并结束时显示diff统计。配置项`merge.stat`也可以用来控制这个统计。  
`--no-stat`或者`-n`将不会输出diff统计。

`-squash`
`--no-squash`
>像真实合并一样修改工作区和暂存区的状态（不记录合并信息），但不做实际提交，不移动`HEAD`指针，也不记录能让下一次`git commit`指令创建合并提交的`$GIT_DIR/MERGE_HEAD`。它允许你在当前分支的基础上创建一次独立的提交，效果和

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
除了快进式合并（参见上面），被合并的分支必须被一个新的合并记录粘合到一起，这个新的合并记录把当前分支和被合并的分支列为父记录。

生成的合并版本融合了所有分支提交的改动，你的版本库的头指针，暂存区及工作区会被更新到这个版本。工作区中有可能有文件修改未提交，这些未提交的修改可以一直保留直到和上游的改动有重合。

如果不能明确地融合这些改动，将会执行这些步骤：
>1. `HEAD`指针保持不变
>2. `MERGE_HEAD`指针指向另外一个分支头
>3. 更新成功合并的文件到暂存区和工作目录
>4. 对于冲突的文件，暂存区记录最多三个版本：1记录共同祖先的版本；2记录`HEAD`的版本；3记录`MERGE_HEAD`的版本（你可以用这个指令`git ls-files -u`审查这些版本）。工作区则记录下“merge”程序执行的结果：例如，三方合并执行的结果是增加了熟悉的冲突标记`<<<===>>>`。（译者注：此处merge非git-merge）
>5. 其它不做改动。合并之前的本地修改将会保留，与之对应的暂存区中的文件也和合并之前保持一致。

如果你尝试的合并导致了一个复杂的冲突结果而想重新开始，你可以通过`git merge --abort`来恢复。

####冲突是怎么呈现的
在合并过程中，工作区文件会被更新以用来反映合并结果。在相对于共同祖先的改动之中，非重叠部分（比如，你修改了文件的一部分，而另一边未修改这部分，诸如此类）会被逐字地组合在一起。然而当两边修改了同一部分，git不能随机选择其中一边而放弃另一边，它会给你留下两边的改动让你来解决。

默认情况下，git会使用和RCS套件中的'merge'(译者注：RCS是另一款版本管理工具，http://www.idi.ntnu.no/~systprog/rcs/) 程序相同的风格来呈现冲突内容，像这样：
```c
Here are lines that are either unchanged from the common
ancestor, or cleanly resolved because only one side changed.
<<<<<<< yours:sample.txt
Conflict resolution is hard;
let's go shopping.
=======
Git makes conflict resolution easy.
>>>>>>> theirs:sample.txt
And here is another line that is cleanly resolved or unmodified.
```
冲突区域被`<<<<<<<`,`=======`和`>>>>>>>`标记出来。`=======`之前的部分是你的修改，之后的部分是其它人的修改。

默认的格式没有显示出原始文件中冲突区域的内容。你不能判断出有多少行被其它人删除/替换。你仅仅知道你这边改成了什么，其它人那边改成了什么。

可以选择在配置中设置`merge.conflictstyle`变量的值为`diff3`，这样上面的冲突会变成这样：
```c
Here are lines that are either unchanged from the common
ancestor, or cleanly resolved because only one side changed.
<<<<<<< yours:sample.txt
Conflict resolution is hard;
let's go shopping.
|||||||
Conflict resolution is hard.
=======
Git makes conflict resolution easy.
>>>>>>> theirs:sample.txt
And here is another line that is cleanly resolved or unmodified.
```
除了`<<<<<<<`,`=======`和`>>>>>>>`标记外，这个指令使用了`|||||||`标记，后面跟着的是原始内容。你可以分辨原始内容，你的改动及其它人的改动。有时根据原始内容能更好地解决冲突。

####怎么解决冲突
当冲突出现时，你可以采取以下两种手段：

- 暂时不合并。你需要做的清理工作是重置暂存区文件到`HEAD`指向的文件状态，以撤消合并时做的第2步，然后撤消工作区第2步和第3步的改动（译者注：原文档可能笔误，这里的第2步应该指合并时的第3步`更新成功合并的文件到暂存区和工作目录`，这里的第3步应该指的合并时的第4步）；`git merge --abort`适用这种场景，用来撤消合并。
- 解决冲突。Git会在工作区中标记冲突，编辑冲突的文件然后使用`git add`把它们加入暂存区，最后使用`git commit`来完成合并。

你还可以借助以下一些工具来解决冲突：

- 使用一款合并工具：`git mergetool`将会打开图形合并工具
- 查看改动：`git diff`将会展示三方改动，并高亮显示来自`HEAD`和`MERGE_HEAD`的改动
- 查看各个分支的改动：`git log --merge -p <path>`将会先展示来自`HEAD`的改动，然后展示来自`MERGE_HEAD`的改动
- 查看原始文件：`git show :1:filename`显示共同祖先的原始文件；`git show :2:filename`显示`HEAD`原始文件；`git show :3:filename`显示`MERGE_HEAD`原始文件

####示例
- 合并`fixes`和`enhancements`到当前分支，来一次多方合并：
```
$ git merge fixes enhancements
```

- 合并`obsolete`到当前分支，使用`ours`合并策略：
```
$ git merge -s ours obsolete
```

- 合并`maint`到当前分支，但不做自动提交：
```
$ git merge --no-commit maint
```
>当你想进一步对合并做改动，或者只是想自己写合并提交日志时，你可以使用这种方法。
>不要滥用这个选项去暗地里做一些重要改动而提交到这次合并中来。小的修补例如修改发布号或版本号是可以接受的。

####合并策略
合并时（`git-merge`和`git-pull`）可以使用`-s`选项来选择底层的`merge strategies`（译者注：合并策略）。有一些合并策略也会有自己的选项，你可以使用`-X<options>`参数传递选项给`git-merge`或`git-pull`。

`resolve`
>这种策略仅能使用三方合并算法处理两个头指针的情况（例如，当前分支和你想要拉下来的分支）。它尝试检查十字交叉合并（译者注：十字交叉合并可以简单地理解为有两个不同的共同祖先，参见：[The criss-cross merge case]( http://www.gelato.unsw.edu.au/archives/git/0504/2279.html#replies)）歧义，通常被认为是快捷安全的。

`recursive`
>这种策略仅能使用三方合并算法处理两个头指针的情况。当存在不只一个共同祖先可以被用作三方合并时，这种策略生成共同祖先的合并树，并且拿它作为三方合并的参考（译者注：相当于生成了虚拟的提交，然后作为三方合并的base）。这种策略在Linux 2.6内核开发历史的实际操作中被印证可生成更少的冲突并且可避免遗漏。另外，这种策略可以正确处理合并引起的重命名。这是拉一个上游分支和合并一个分支的默认策略。

>`recursive`策略支持以下选项：

>`ours`
>>这个选项强制自动解决冲突块，优先选择`our`版本。另一分支树上的改动如果没有和当前分支冲突，那么这些改动就会被保留下来。如果是二进制文件，直接使用当前分支版本。  
不要把这个选项和`ours`合并策略搞混淆了，`ours`合并策略根本不会去查看其它分支树上的改动。`ours`合并策略会遗弃其它分支树的改动并声称自己的历史已经包含了那些改动。

>`theirs`
>>这个选项和`ours`相反

>`patience`
>>加上这个选项，`merge-recursive`会花一点点额外的时间来避免遗漏，这些遗漏有时发生在不重要的行匹配上（比如，不同函数的花括号）。当要合并的分支已经分离很久时，可以使用这个选项。参见`git-diff(1) --patience`。

>`ignore-space-change`  
>`ignore-all-space`  
>`ignore-space-at-eol`
>>在三方合并时忽略上述类型的行内空白的修改。一行中混合了空白字符及其它修改将不会被忽略。参见：`git-diff(1) -b -w --ignore-space-at-eol`。  

>>- 如果一行中`their`版本仅仅引入空白修改，将采用`our`版本  

>>- 如果一行中`our`版本仅仅引入空白修改，而`their`版本不仅仅只有空白修改，将采用`their`版本  

>>- 否则，将进行正常合并

>`renormalize`
>>这个选项在三方合并时对文件的三个状态（译者注：base,our,their）执行虚拟的签入签出。这意味着可以用来合并包含不同的清理过滤器和不同的文件结束符规则的分支。详细请参见`gitattributes(5)`中的“合并包含不同签入/签出属性的分支”

>`no-renormalize`
>>禁用`renormalize`选项。这个选项也会覆盖`merge.renormalize`配置项。

>`rename-threshold=<n>`
>>控制重命名检测中的的相似度阈值。参见`git-diff(1) -M`

>`subtree[=<path>]`
>>这个选项是`subtree`策略的高级形式，`subtree`策略会在合并过程中猜测着去移动两棵树以让它们互相匹配。与之不同的是，这个选项可以指定一个路径前缀来让两棵树匹配。

octopus
>这种策略适用多于两个分支合并的场景，不允许手工解决复杂的冲突。这主要意味着可以用来绑定多个特性分支。它是拉多个上游分支或合并多个分支的默认策略。

ours
>这种策略可以解决任意数量的分支合并，但合并的结果树永远是当前分支树，也就是会忽略所有来自其它分支的改动。这意味着当前分支可以用来取代旧的旁支历史分支。注意了，这种策略和`recursive`策略的`-Xours`选项是有区别的。

subtree
>这是调整过的`recursive`策略。当合并A树和B树时，如果B树对应A树的子树，那么B树会先和A树的子树结构进行匹配而不是直接和A树匹配。这种子树匹配调整同样适用于共同祖先树。

####配置项

####参考
`git-fmt-merge-msg(1)`,`git-pull(1)`,`gitattributes(5)`,`git-reset(1)`,`git-diff(1)`,`git-ls-files(1)`,`git-add(1)`,`git-rm(1)`,`git-mergetool(1)`
####GIT
`git`指令的一部分。

> Written with [StackEdit](http://benweet.github.io/stackedit/).