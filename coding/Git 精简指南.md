> _Git 是 Linus Torvalds 为了帮助管理 Linux 内核代码而开发的一个开放源码的版本控制软件。_

Git 是一款非常强大的版本控制工具，在开发过程中发挥着十分重要的作用，它有许多优点：

1. 分布式版本控制，既可以在本地进行管理也可以在远端协同作业。
2. 优秀的分支机制，让代码管理更加容易。
3. 执行速度快，性能高，文件占用空间小。
4. 开源免费，任何人都可以使用和修改。

虽然 Git 非常的强大，有很多优点，但是它的问题是易学难精，在日常工作可能只需要 20% 的指令就可以完成 80% 的任务，也就是说很容易上手但是想用的好还是需要花时间去钻研。
## 初始化设置
在安装好 Git 后，一般需要在命令行下测试一下：
```bash
$ which git // 查找安装目录
$ git --version  // 查看git版本
```
在使用 Git 前，首要就要设置用户名和用户邮箱，可以设置全局用户名和邮箱，也可以给当前项目设置独立的用户名和邮箱，通常设置全局。
```bash
$ git config --global user.name "Tecladochen"  // 设置全局用户名
$ git config --global user.email "Tecladochen@gmail.com"  // 设置全局邮箱

$ git config --list  // 查看设置列表

$ git config --local user.name "others"  // 设置局部用户名
$ git config --local user.email "others@gmail.com"  // 设置局部邮箱
```
根据个人偏好可以设置不同的编辑器，默认是 Vim。
```bash
	$ git config --global core.editor emacs  // 设置默认编辑器
```
设置命令缩写，常用的命令使用简写可以减少很多时间花费，所有的缩写设置可在 ~/.gitconfig 中修改查看。
```bash
$ git config --global alias.st status  // git st 等于 git status
$ git config --global alias.br branch  // git br 等于 git branch

$ git config --global alias.l "log --oneline --graph"
// git l 等于 git log --oneline --graph
```
## 暂存和提交
Git 版本控制从初始化开始。
```bash
$ git init // 初始化命令
$ git add cat.html  // 提交暂存区
```
`.`和`--all`都表示全部文件，在当前所处位置不同时会有区别。
```bash
$ git add .  // 暂存当前目录所有文件
$ git add --all  // 暂存仓库所有文件
```
提交前查看文件状态信息，也是是 Git 中最常用的命令。
```bash
$ git status  // 查看文件状态
```
提交操作生成版本号，`-m`参数后面接一段文本信息作为本次提交说明；
`--allow-empty`参数表示允许本次提交内容为空，也就是没有任何改动就提交。
```bash
$ git commit -m "init commit"
$ git commit --allow-empty -m "可以是空"
```
暂存和提交操作合并，一次完成，但是无法提交第一次加进来为被追踪的文件。
```bash
$ git commit -a -m "update content"
```
在 Git 管理的项目中 .gitignore 和 .gitkeep 是俩个特殊文件，它们有特殊的作用。

1. 在 .gitignore 文件中写入内容表示 git 不追踪哪些文件。
2. 在一个空文件夹中放置一个 .gitkeep 空文件来表示占位。
## 查看记录
使用`git log`命令查看提交记录，越近的信息会越靠上。配合参数实现更多查询。
```bash
$ git log  // 查看基本 commit 信息
$ git log --oneline --graph  // 单行显示并显示图示
$ git log --oneline --author="Tecladochen"  // 查看特定作者的记录
$ git log --oneline --since="9am" --until="12am" --after="2017-01"
// 查看特点日期和时间段的提交信息
```
另外一种方式是使用 reflog 命令查看日志记录，可以查到所有关键操作记录。
```bash
$ git reflog
```
## 删除和修改
在 Git 中删除也是一种变动，删除文件需要暂存变动信息。
```bash
$ rm cat.html  
$ git add cat.html
$ git rm cat.html  // 实现上面俩步的功能
```
`--cached`参数让文件在不被删除的情况下不被追踪，并提交变动信息，相当于工作目录文件还在，暂存区提交删除变动信息。
```bash
$ git rm cat.html --cached
```
在 Git 中文件重命名，相当于一次做了俩个操作，删除了原来名字的文件，新加一个当前名字的文件，所以在改名后，要提交所有改动信息，暂存区才识别出改名操作。
```bash
$ mv cat.html dog.html  // 修改 cat 文件名为 dog
$ git add --all  // 暂存删除和新增变动信息
$ git mv cat.html dog.html  // 实现上面俩步的功能
```
## 版本回退
在 Git 中你可以选择回到任何一个 commit 状态，这给你无限反悔的机会。
使用 reset 命令指定要回到的 commit 节点，Git 会把指定节点之后提交的文件恢复到指定的位置上。指定节点时，HEAD 表示当前所在节点，使用 HEAD^ 表示当前节点的前一个节点，也就意味着回退到上一个 commit ，当前 commit 提交的文件和修改会被重新放回指定的位置；而 HEAD~2 表示当前节点前 2 个节点，在 ~ 后可以接想要的数字。
```bash
$ git reset e12di3f
$ git reset HEAD^
$ git reset HEAD~2
```
在回退 commit 时，reset 命令很常用，它可以指定拆出来的文件放回什么位置，默认是 mixed 模式，将拆出来的文件放到工作目录里；使用 --soft 模式将把拆出来的文件放在暂存区里；使用 --hard 将把拆出来的文件直接丢弃。
```bash
$ git reset e12di3f
$ git reset e12di3f --soft
$ git reset e12di3f --hard
```
> 注意：谨慎使用 --hard 模式，它会丢弃对文件的修改，如果在使用 hard 模式后需要找回文件，可以查看 reflog 然后 reset 回到之前的 commit。

在回退 commit 时还有一个 revert 命令可以使用，不同于 reset 命令拆掉不要的 commit 回到指定的 commit，revert 的做法是再创建一个新的 commit 来执行一个  commit 反向操作来抵消它的操作，也达到了回到指定 commit 状态的目的，它的好处是通过提交反向操作来回退提交记录，适合远端协作场景方便其他人了解状态的变化。
```bash
$ git revert HEAD --no-edit
```
Git 会在执行版本回退等一些有风险的操作时，提供一个危险操作返回记录点 ORIG_HEAD，如果操作和自己预期的不同可以通过这个记录点回到操作前状态。
```bash
$ git reset ORIG_HEAD --hard
```
## 修改记录
当代码提交记录越来越繁杂的时候，需要定期做一些提交记录的整理，以确保更高效简洁的管理项目代码。关于修改和调整 commit 记录，Git 为我们提供了强大的功能。
当需要调整最近的一次 commit 信息时，通常使用 amend 参数来管理，通过 amend 参数可以追加文件到上一次 commit 中，也可以修改上一次 commit 的提交信息。
```bash
$ git add new.txt
$ git commit --amend --no-edit  // 追加文件到上一次 commit
$ git commit --amend -m "new edit message"  // 修改上一次 commit 提交信息
```
除了使用 amend 参数修改 commit 记录，Git 提供了一个 rebase 交互模式来调整修改 commit 记录，根据你的需要来重新计算 commit 提交记录。
rebase 交互模式通过 -i 参数开启，后面的 commit 节点号表示需要调整的记录范围，从当前节点到指定节点，不包括指定那个节点。可以理解为在指点节点上开始重新调整，指点节点是不变的。
```bash
$ git rebase -i ad7f4c6
```
运行指令后会进入 vim 编辑器，从上往下每行表示从指点节点到当前节点，可以通过修改第一个操作命令来指定要对这个范围内的节点做什么样的调整。
```bash
pick bb5434d add data.txt
pick ce6a687 add fun1.txt
pick 9fa7ea6 add fun2.txt
pick 366b942 add util.txt
pick e923bdc add view1.txt
pick edb57c6 add view2.txt

# Rebase ad7f4c6..edb57c6 onto ad7f4c6 (6 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
```
之所以说 Git 有强大的修改记录能力就是因为 rebase 交互模式可以做的太多，简单介绍一下一些基本的修改，以后遇到特定需求可以再去深入。
修改多条提交信息，只需要把要修改信息的节点前的 pick 改成 reword，然后存档离开，之后会再弹出 vim 编辑页面，把修改后的信息内容写入存档离开，然后 Git 会自动重新计算整个提交记录。注意的是，reword 多个节点就会弹出对应个数的 vim 页面，按顺序填写存档退出就可以。
```
pick bb5434d add data.txt
reword ce6a687 add fun1.txt
reword 9fa7ea6 add fun2.txt
pick 366b942 add util.txt
pick e923bdc add view1.txt
pick edb57c6 add view2.txt
```
有时候 commit 过于琐碎，需要把多个 commit 合并为一个 commit，需要使用 squash 命令，它能够后面的节点合并到前面的节点。下面的配置保存退出后，Git 会把添加 fun1 和 fun2 的俩次 commit 合并到前面的提交 data 的 commit 中，还会弹出一个 vim 编辑页面来重新填写合并后的提交信息。
```
pick bb5434d add data.txt
squash ce6a687 add fun1.txt
squash 9fa7ea6 add fun2.txt
pick 366b942 add util.txt
pick e923bdc add view1.txt
pick edb57c6 add view2.txt
```
有时候单词 commit 提交的文件太多了，需要把一个 commit 拆分成多个 commit，这时候需要使用 edit 命令，保存退出后 Git 会重新计算并在需要 edit 的节点停下来。
```
pick bb5434d add data.txt
pick ce6a687 add fun1.txt
pick 9fa7ea6 add fun2.txt
edit 366b942 add util.txt
pick e923bdc add view1.txt
pick edb57c6 add view2.txt
```
在停下来后，因为要把当前 commit 拆成俩个 commit，所以要先 reset 这个 commit，然后重新提交 commit。重新提交结束后再让 rebase 继续计算。
```bash
$ git reset HEAD^
$ git add util1.txt
$ git commit -m "add util1.txt"
$ git add util2.txt
$ git commit -m "add util2.txt"
$ git rebase --continue
```
最后，如果想要删除某几个 commit 或者调整 commit 的顺序，在 rebase 中会非常容易，只需要在配置页面直接删除 commit 记录行，以及直接调整记录行的顺序，保存退出后不需要别的操作 Git 会自动重新计算实现删除和调整。
```bash
pick bb5434d add data.txt
pick 366b942 add util.txt
pick edb57c6 add view2.txt
pick e923bdc add view1.txt
```


## 使用分支
Git 拥有强大的分支功能，学会使用分支对于掌握 Git 来说至关重要，先了解一下分支的基本用法，之后再深入介绍一下分支的原理。
```git
$ git branch                 // 查看有哪些分支，以及当前在哪个分支上
$ git branch cat             // 新增一个叫 cat 的分支
$ git branch -m cat pig      // 修改 cat 分支名字为 pig
$ git branch -d pig          // 删除 pig 分支
```
> 在删除分支前请确保该分支已经被合并，不然会导致该分支内容的丢失

当创建了分支，需要做的是切换到新建的分支上，切换分支使用 checkout 命令，这个命令对于理解 Git 的分支机制很重要，需要展开讲解一下。
checkout 的本意是检出的意思，也就是说它的功能是把某个 commit 的内容检出或者说恢复到暂存区和工作区。
```git
$ git checkout dog  // 从当前分支切换到 dog 分支
$ git checkout -b pig 
```
> `-b`参数可以指定一个不存在的分支，然后先创建这个分支再切换过去。

在切换分支的过程中，Git 主要做了俩件事：

1. 将要切换的分支指向的那个 commit 中的内容检出或说恢复到暂存区及工作区
2. 变更 HEAD 的位置，指向当前切换后的分支节点

在理解了 checkout 检出命令后，就能理解当 checkout 命令后面跟的是文件名而不是分支名时，Git 会把该文件从指定的 commit 中恢复回来。
```git
$ git checkout hello.txt          // 从当前的 commit 中恢复文件
$ git checkout 3d5ef13 hello.txt  // 从指定的 commit 中恢复文件
$ git checkout 8oe9f29 .				  // 从指定的 commit 中恢复所有文件
```
在了解完分支的基本操作后，就要去涉及分支管理的难点，关于分支合并和冲突解决。
例如，当我们在 master 分支上创建 dev 分支并提交俩次 commit ，那么 dev 分支就领先 master 分支，如果这时候需要让 master 分支合并 dev 分支的内容，其实非常容易，因为master 分支与 dev 分支只有这俩次 commit 内容的不同，master 分支只需要直接收割 dev 分支的 commit 就能和 dev 分支的内容一致，这就是快转模式合并。
```bash
$ git checkout master
$ git merge dev
```
![清新配色流程图标准样式模板.png](https://cdn.nlark.com/yuque/0/2023/png/12428058/1699236030284-817197ff-4f88-4466-896d-733786afc2d1.png#averageHue=%23020101&clientId=uc8525733-8d8d-4&from=paste&height=384&id=u3a615533&originHeight=384&originWidth=519&originalType=binary&ratio=1&rotation=0&showTitle=false&size=14700&status=done&style=none&taskId=u39f4faf4-d37d-4b1e-909e-3c6189adc9a&title=&width=519)
快转模式理解起来比较简单，但是开发过程通常会有多个分支在开发，当从 master 分支上分出俩个开发不同功能的分支时，例如，cat 分支需要合并 dog 分支的内容时，就无法使用快转模式，只能通过创建一个额外 commit 节点来记录合并的信息。
> 在 master 分支可以收割内容的情况也可以使用额外节点模式，额外节点模式可以清晰的记录分支合并的操作记录

```bash
$ git merge dog
$ git merge dev --no-ff
```
![清新配色流程图标准样式模板 (1).png](https://cdn.nlark.com/yuque/0/2023/png/12428058/1699236494161-f1885ee6-06b1-46c7-bd09-a09ab70fa2d4.png#averageHue=%23010101&clientId=uc8525733-8d8d-4&from=paste&height=512&id=udee5eb5d&originHeight=512&originWidth=841&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23712&status=done&style=none&taskId=u9460a583-2578-4fd3-894b-9985a9afa07&title=&width=841)
合并分支还有一种方式，称为 rebase 模式，不同于普通合并分支，rebase 合并分支不创建额外的 commit 节点，rebase 的意思就是以新的分支作为基准来达到合并分支的目的。例如，cat 分支要合并 dog 分支的内容，git 会在 dog 分支的基础上重新计算 cat 分支提交的内容生成新的节点信息。
```bash
$ git rebase dog
```
![清新配色流程图标准样式模板 (2).png](https://cdn.nlark.com/yuque/0/2023/png/12428058/1699346688460-d1bdfb41-e965-4956-bc45-5901c1ef9475.png#averageHue=%23010000&clientId=ub1b47c6c-b468-4&from=paste&height=573&id=u2de08b40&originHeight=573&originWidth=841&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25817&status=done&style=none&taskId=u986d662e-95a6-4c33-8ab6-dfed46d960b&title=&width=841)
无论使用哪种合并模式，当分支出现冲突时都必须解决冲突才能继续合并，解决冲突合并的方式就是人为选择使用谁的版本作为合并后的版本。
在普通合并模式中，当合并分支产生冲突时，git 会把俩个版本的不同修改到文件中，等待你删除留下其中一个版本，然后再使用 add 和 commit 提交合并过程。在 rebase 模式中，产生冲突时也会中断处理，也是修改冲突文件然后提交到暂存区，使用 continue 命令继续 rebase 过程。
```bash
$ git merge dev
$ git add conflict.txt
$ git commit -m "merge dev"
```
```bash
$ git rabase dev
$ git add conflict.txt
$ git rebase --continue
```
当产生冲突的文件是二进制文件时，可以指定选用谁的文件作为合并后的版本。
```bash
$ git checkout --ours binary.jpg
$ git checkout --theirs binary.jpg
```
## 使用标签
在 Git 中，标签（Tag）是指向某个 commit 的指示标，通常用来做开发中完成特定功能的里程碑。标签有俩种：轻量标签和有附注的标签。
轻量标签的使用方法很简单，`git tag`命令后面接标签名字表示为当前 commit 贴上一个轻量标签，或者在名字后面指定要贴标签的 commit 号。
```git
$ git tag cat
$ git tag cat 51fd54e
```
创建一个有附注的标签也很简单，前部分和轻量标签的使用方式一样，`-a`表示创建一个附注的标签，`-m`表示使用后面的字符串作为附注。
```git
$ git tag dogs 52ef8ff -a -m "there are many dogs"
```
俩种标签的区别就是带有的信息量不同，有附注的标签带有更多的信息。通常的做法是，有附注的标签用作软件版本号，而轻量标签用于个人使用和暂时标记。
标签和贴纸一样，在不需要的时候可以删除不会对内容有任何影响
```git
$ git show dogs  // 查看标签的信息
$ git tag -d cat  // 删除标签
```
标签和分支的区别：标签和分支的存在方式很像，都像贴纸一样贴在某个 commit 上，不同的是，标签在创建后就一直贴在某个 commit 上，不会再变动；而分支是会随着 commit 的提交向前移动的。
## 远端协作
Git 可以配合 Git 服务器实现远程管理和团队写作，我们熟知的 Github 就是基于 Git 服务器的商业网站。以 Github 为例：
想要将本地的仓库 push 到远端，首先是要到 Github 上创建一个仓库。
然后在本地仓库中，添加远端节点：
```git
$ git remote add origin git@github.com:xxx/test.git
```
`git remote`表示是与远端有关的操作，`add`表示要加入一个远端的节点，`origin`是一个名字用来代指远端的节点地址。在 git 中，`origin`是代指远端节点的约定俗成的默认值，你也可以设置你想要的不同的值。
```git
$ git remote add myRemote git@github.com:xxx/test.git
```
在本地添加远端节点后，就可以开始将本地仓库推送到远端：
```git
$ git push -u origin master  // 等价于
$ git push -u origin master:master  
```
`git push`命令表示要推送本地到远端，`origin`表示要推送到哪个远端节点，`master`表示将本地的 master 分支推送到远端的 master。也可以推送到远端不同名的分支，也可以通过这种方法来删除远端分支。
```git
$ git push origin master:dev  
// 将本地的 master 分支推送到 origin 节点的 dev 分支上
$ git push origin :dev
// 将一个空分支推送到 origin 节点的 dev 分支上
// 等价于，删除 origin 节点的 dev 分支
```
在说明一下 -u 参数的作用，`-u`表示将 origin 的 master 分支作为本地 master 分支的上游，在第一次推送时带上这个参数，之后的推送就可以不用在指明推送目标。
```git
$ git push origin master  // 不指定上游，每次需要指定推送目标
$ git push  // 指定上游，可以直接推送
```
在团队协作中，通常有多个人往远端分支上推送内容，所以在我们推送新内容前通常需要先拉取远端分支的最新内容再进行合并，然后才能顺利推送上去。
在 git 中，有一个 fetch 命令用来从远端分支拉取内容：
```git
$ git fetch
$ git merge origin/master
```
通常的流程就是将远端的分支拉取下来，将其与当前分支合并，于是 git pull 这个命令就是将这俩步合并成一步。
```git
$ git pull  // 等价于 git fetch + git merge
```
在日常的工作流中，推送（push），拉取（pull）俩个操作是最常用的，另外还有一些会用到的命令。
`git clone`命令会把一个远端仓库复制一份到本地，默认放在与仓库同名的文件夹内，也可以手动更改文件夹名字。
```git
$ git clone git@github.com:xxx/test.git  
$ git clone git@github.com:xxx/test.git myTest  // 手动设置文件夹名字
```
> 克隆下来的仓库包括整个项目的历史记录、分支、标签信息等。

Github 作为代码管理平台，也是开发者互动的社区，开发者通常会通过对感兴趣的项目提出 pr 和 issue 来帮助原作者完善项目。简单介绍一下提交PR的流程：

1. 先 fork 一份原作者的项目到自己的账号下对 ，fork 下来的项目拥有完整的权限，可以对它进行改进。
2. 将自己账号下的项目克隆到本地，先运行起来，再修改代码
3. 修改完成后，先拉取原作者的项目代码做合并，然后再推送到自己的账号下
4. 在项目仓库上点击新建 Pull Request，并提交希望合并的分支
5. 原作者查看 PR，进行 Code Review，如果没问题原作者同意并将分支合并进源项目

