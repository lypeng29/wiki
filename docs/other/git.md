# git学习笔记
工作区--》暂存区--》本地版本库--》远程版本库

## 常规操作
```
git init 						//初始化
git status 						//查看当前git状态 
git add a.txt 					//添加文件
git rm b.txt 					//删除文件
git commit -m 'some message' 	//提交到本地仓库
git clone 						//克隆远程仓库
git push 						//推送到远程仓库
git pull 						//拉取
```

## 配置

### 用户信息
```
git config --global user.name "lypeng"
git config --global user.email "893371810@qq.com"
```

### 区分大小写
`git config core.ignorecase false`

### 创建ssh-key密钥
执行`ssh-keygen -t rsa -C "893371810@qq.com"`，一路回车，然后将id_rsa.pub复制到github中

### 远程操作
先创建ssh key之后再来远程操作，采用ssh方式克隆，而不是https，这样就不用每次都输入密码了
```
git remote -v //查看远程信息是不是ssh方式的，如果不是删了，重建
git clone git@github.com:lypeng29/baike_spider
git push
```

## 版本回退

### 本地回退
#### 1.执行了git add，没有执行git commit，即从暂存区清理掉
`git reset a.txt` 

#### 2.执行了git add，并且执行git commit，HEAD^可以是具体版本号
```
回退所有内容到上一个版本　　　　
git reset HEAD^

回退a.txt这个文件的版本到上一个版本
git reset HEAD^ a.txt

将本地的状态回退到和远程的一样
git reset --hard origin/master

```

### 远程的回退（执行了commit与push）
```
git push origin HEAD --force
//或者
git reset --hard HEAD~1
git push --force
```

## 远程操作
```
//查看远程信息
git remote -v

//添加远程主机名为origin，也可以为其他 
git remote add origin git@github.com:lypeng29/wiki.git

//写错了可以删除远程主机名
git remote rm origin

git push <远程主机名> <本地分支名>:<远程分支名> 
git push origin master:master //推送本地版本库到远程master分支,后面的:master可以不写， 

强制用远程文件覆盖本地文件
git fetch --all
git reset --hard origin/master

//关于创建tag版本信息
git tag v1.0
git tag -a v1.0 -m "first version" //普通带注释的tag
git tag -d v1.0 //删除
git push origin --tag	//tag信息，需单独推送
```

## 关于分支

### 基础操作
```
//查看分支列表
git branch

//创建dev分支
git checkout -b dev master 

//推送dev到远程，如果远程分支不存在则会自动创建
git push origin dev:dev 

//合并dev到master分支
git checkout master 
git merge --no-ff dev
```

> 默认情况下，Git执行"快进式合并"（fast-farward merge），会直接将Master分支指向Develop分支。使用--no-ff参数后，会执行正常合并，在Master分支上生成一个新节点。为了保证版本演进的清晰，我们希望采用这种做法。参考文章后面更多命令里的动图


### 功能分支

为了开发某种特定功能，从Develop分支上面分出来的。开发完成后，要再并入Develop。
功能分支的名字，可以采用feature-* 的形式命名。

```
// 从开发分支上，创建一个功能分支：
git checkout -b feature-x dev

// 开发完成后，将功能分支合并到dev分支：
git checkout dev
git merge --no-ff feature-x

// 删除feature分支：
git branch -d feature-x
```

### 预发布分支release

指发布正式版本之前（即合并到Master分支之前），我们可能需要有一个预发布的版本进行测试。
预发布分支是从dev分支上面分出来的，预发布结束以后，必须合并进Develop和Master分支。它的命名，可以采用release-* 的形式。

```
// 创建一个预发布分支：
git checkout -b release-1.2 dev

// 确认没有问题后，合并到master分支：
git checkout master
git merge --no-ff release-1.2

// 对合并生成的新节点，做一个标签
git tag -a 1.2

// 再合并到dev分支：
git checkout dev
git merge --no-ff release-1.2

// 最后，删除预发布分支：
git branch -d release-1.2
```

### bug分支
```
// 创建一个修补bug分支：
git checkout -b fixbug-0.1 master

// 修补结束后，合并到master分支：
git checkout master
git merge --no-ff fixbug-0.1
git tag -a 0.1.1

// 再合并到dev分支：
git checkout dev
git merge --no-ff fixbug-0.1

// 最后，删除"修补bug分支"：
git branch -d fixbug-0.1
```

## git 修改已提交的注释
原文地址：https://www.cnblogs.com/zhangjianbin/p/10126190.html

在git中，其commit提供了一个--amend参数，可以修改最后一次提交的信息
修改最后一次提交注释 git commit --amend
然后在出来的编辑界面，直接编辑注释的信息,保存退出

`git rebase -i HEAD~3`
git使用amend选项提供了最后一次commit的反悔。但是对于历史提交呢，就必须使用rebase了。
修改push后的历史提交注释

这个命令出来之后，会出来三行东东：
```
pick:*******

pick:*******

pick:*******  
```
如果你要修改哪个，就把那行的pick改成edit，然后保存退出。
git commit --amend
修改注释

git rebase --continue
合并注释

git log
查看提交记录

提交修改后的注释
git pull origin master
git push origin master


## fork了别人的仓库，怎么更新到最新版本
```
git remote add upstream https://github.com/aaa/bbb.git (把别人的仓库添加到upstream)
git fetch upstream（从原仓库拉取）
git checkout master （切换到本地master）
git merge upstream/master（合并拉取的内容）
git push origin master（推送到自己git）
```


## 更多命令(转自程序员的那些事)

英文链接：
https://dev.to/lydiahallie/cs-visualized-useful-git-commands-37p1

【导读】：git merge、git rebase、git reset、git revert、git fetch、git pull、git reflog……你知道这些 git 命令执行的究竟是什么任务吗？如果你还有些分不清楚，那千万不能错过这篇文章。

在本文中，熟知 JavaScript、TypeScript、GraphQL、Serverless、AWS、Docker 和 Golang 的 21 岁年轻软件顾问 Lydia Hallie 通过动图形式直观地介绍了这些常用 git 命令的工作过程，包你过目不忘。

尽管 Git 是一款非常强大的工具，但如果我说 Git 用起来简直是噩梦，大多数人也会认同我的说法。我发现在使用 Git 时，在头脑里可视化地想象它会非常有用：当我执行一个特定命令时，这些分支会如何交互，又会怎样影响历史记录？为什么当我在 master 上执行硬重启，force push 到原分支以及 rimraf 我们的 .git 文件夹时，我的同事哭了？

我觉得创建一些最常用且最有用的 Git 命令的可视化示例会是一个完美的用例！下面我将介绍的很多命令都有可选参数——你可以使用这些参数来改变对应命令的行为。而我的示例只会涵盖命令的默认行为，而不会添加（或添加太多）可选配置！

![](http://img.lypeng.com/wiki/git/0.jpg)

本文作者Lydia Hallie

合并

拥有多个分支是很方便的，这样可以将不同的新修改互相隔离开，而且还能确保你不会意外地向生产代码推送未经许可或破损的代码修改。但一旦这些修改得到了批准许可，我们就需要将其部署到我们的生产分支中！

可将一个分支的修改融入到另一个分支的一种方式是执行 git merge。Git 可执行两种类型的合并：fast-forward 和 no-fast-forward。现在你可能分不清，但我们马上就来看看它们的差异所在。

Fast-forward (—ff)

在当前分支相比于我们要合并的分支没有额外的提交（commit）时，可以执行 fast-forward 合并。Git 很懒，首先会尝试执行最简单的选项：fast-forward！这类合并不会创建新的提交，而是会将我们正在合并的分支上的提交直接合并到当前分支。


![](http://img.lypeng.com/wiki/git/1.gif)

完美！现在，我们在 dev 分支上所做的所有改变都合并到了 master 分支上。那么 no-fast-forward 又是什么意思呢？

No-fast-foward (—no-ff)

如果你的当前分支相比于你想要合并的分支没有任何提交，那当然很好，但很遗憾现实情况很少如此！如果我们在当前分支上提交我们想要合并的分支不具备的改变，那么 git 将会执行 no-fast-forward 合并。

使用 no-fast-forward 合并时，Git 会在当前活动分支上创建新的 merging commit。这个提交的父提交（parent commit）即指向这个活动分支，也指向我们想要合并的分支！


![](http://img.lypeng.com/wiki/git/2.gif)

没什么大不了的，完美的合并！现在，我们在 dev 分支上所做的所有改变都合并到了 master 分支上。

合并冲突

尽管 Git 能够很好地决定如何合并分支以及如何向文件添加修改，但它并不总是能完全自己做决定。当我们想要合并的两个分支的同一文件中的同一行代码上有不同的修改，或者一个分支删除了一个文件而另一个分支修改了这个文件时，Git 就不知道如何取舍了。

在这样的情况下，Git 会询问你想要保留哪种选择？假设在这两个分支中，我们都编辑了 README.md 的第一行。

![](http://img.lypeng.com/wiki/git/3.png)

如果我们想把 dev 合并到 master，就会出现一个合并冲突：你想要标题是 Hello! 还是 Hey!？

当尝试合并这些分支时，Git 会向你展示冲突出现的位置。我们可以手动移除我们不想保留的修改，保存这些修改，再次添加这个已修改的文件，然后提交这些修改。

![](http://img.lypeng.com/wiki/git/4.gif)

完成！尽管合并冲突往往很让人厌烦，但这是合理的：Git 不应该瞎猜我们想要保留哪些修改。



变基（Rebasing）

我们刚看到可通过执行 git merge 将一个分支的修改应用到另一个分支。另一种可将一个分支的修改融入到另一个分支的方式是执行 git rebase。

git rebase 会将当前分支的提交复制到指定的分支之上。


![](http://img.lypeng.com/wiki/git/5.gif)

完美，现在我们在 dev 分支上获取了 master 分支上的所有修改。

变基与合并有一个重大的区别：Git 不会尝试确定要保留或不保留哪些文件。我们执行 rebase 的分支总是含有我们想要保留的最新近的修改！这样我们不会遇到任何合并冲突，而且可以保留一个漂亮的、线性的 Git 历史记录。

上面这个例子展示了在 master 分支上的变基。但是，在更大型的项目中，你通常不需要这样的操作。git rebase 在为复制的提交创建新的 hash 时会修改项目的历史记录。

如果你在开发一个 feature 分支并且 master 分支已经更新过，那么变基就很好用。你可以在你的分支上获取所有更新，这能防止未来出现合并冲突。

交互式变基（Interactive Rebase）

在为提交执行变基之前，我们可以修改它们！我们可以使用交互式变基来完成这一任务。交互式变基在你当前开发的分支上以及想要修改某些提交时会很有用。

在我们正在 rebase 的提交上，我们可以执行以下 6 个动作：

reword：修改提交信息；

edit：修改此提交；

squash：将提交融合到前一个提交中；

fixup：将提交融合到前一个提交中，不保留该提交的日志消息；

exec：在每个提交上运行我们想要 rebase 的命令；

drop：移除该提交。



很棒！这样我们就能完全控制我们的提交了。如果你想要移除一个提交，只需 drop 即可。
![](http://img.lypeng.com/wiki/git/6.gif)



如果你想把多个提交融合到一起以便得到清晰的提交历史，那也没有问题！
![](http://img.lypeng.com/wiki/git/7.gif)



交互式变基能为你在 rebase 时提供大量控制，甚至可以控制当前的活动分支。

重置（Resetting）

当我们不想要之前提交的修改时，就会用到这个命令。也许这是一个 WIP 提交或者可能是引入了 bug 的提交，这时候就要执行 git reset。

git reset 能让我们不再使用当前台面上的文件，让我们可以控制 HEAD 应该指向的位置。

软重置

软重置会将 HEAD 移至指定的提交（或与 HEAD 相比的提交的索引），而不会移除该提交之后加入的修改！

假设我们不想保留添加了一个 style.css 文件的提交 9e78i，而且我们也不想保留添加了一个 index.js 文件的提交 035cc。但是，我们确实又想要保留新添加的 style.css 和 index.js 文件！这是软重置的一个完美用例。



![](http://img.lypeng.com/wiki/git/8.gif)

输入 git status 后，你会看到我们仍然可以访问在之前的提交上做过的所有修改。这很好，这意味着我们可以修复这些文件的内容，之后再重新提交它们！



硬重置

有时候我们并不想保留特定提交引入的修改。不同于软重置，我们应该再也无需访问它们。Git 应该直接将整体状态直接重置到特定提交之前的状态：这甚至包括你在工作目录中和暂存文件上的修改。

![](http://img.lypeng.com/wiki/git/9.gif)



Git 丢弃了 9e78i 和 035cc 引入的修改，并将状态重置到了 ec5be 的状态。



还原（Reverting）

另一种撤销修改的方法是执行 git revert。通过对特定的提交执行还原操作，我们会创建一个包含已还原修改的新提交。

假设 ec5be 添加了一个 index.js 文件。但之后我们发现其实我们再也不需要由这个提交引入的修改了。那就还原 ec5be 提交吧！


![](http://img.lypeng.com/wiki/git/10.gif)

完美！提交 9e78i 还原了由提交 ec5be 引入的修改。在撤销特定的提交时，git revert 非常有用，同时也不会修改分支的历史。

拣选（Cherry-picking）

当一个特定分支包含我们的活动分支需要的某个提交时，我们对那个提交执行 cherry-pick！对一个提交执行 cherry-pick 时，我们会在活动分支上创建一个新的提交，其中包含由拣选出来的提交所引入的修改。


假设 dev 分支上的提交 76d12 为 index.js 文件添加了一项修改，而我们希望将其整合到 master 分支中。我们并不想要整个 dev 分支，而只需要这个提交！


![](http://img.lypeng.com/wiki/git/11.gif)

现在 master 分支包含 76d12 引入的修改了。


取回（Fetching）

如果你有一个远程 Git 分支，比如在 GitHub 上的分支，当远程分支上包含当前分支没有的提交时，可以使用取回。比如当合并了另一个分支或你的同事推送了一个快速修复时。

通过在这个远程分支上执行 git fetch，我们就可在本地获取这些修改。这不会以任何方式影响你的本地分支：fetch 只是单纯地下载新的数据而已。


![](http://img.lypeng.com/wiki/git/12.gif)

现在我们可以看到自上次推送以来的所有修改了。这些新数据也已经在本地了，我们可以决定用这些新数据做什么了。


拉取（Pulling）

尽管 git fetch 可用于获取某个分支的远程信息，但我们也可以执行 git pull。git pull 实际上是两个命令合成了一个：git fetch 和 git merge。当我们从来源拉取修改时，我们首先是像 git fetch 那样取回所有数据，然后最新的修改会自动合并到本地分支中。


![](http://img.lypeng.com/wiki/git/13.gif)

很好，我们现在与远程分支完美同步了，并且也有了所有最新的修改！

Reflog

每个人都会犯错，但犯错其实没啥！有时候你可能感觉你把 git repo 完全搞坏了，让你想完全删了了事。

git reflog 是一个非常有用的命令，可以展示已经执行过的所有动作的日志。包括合并、重置、还原，基本上包含你对你的分支所做的任何修改。


![](http://img.lypeng.com/wiki/git/14.gif)

如果你犯了错，你可以根据 reflog 提供的信息通过重置 HEAD 来轻松地重做！

假设我们实际上并不需要合并原有分支。当我们执行 git reflog 命令时，我们可以看到这个 repo 的状态在合并前位于 HEAD@{1}。那我们就执行一次 git reset，将 HEAD 重新指向在 HEAD@{1} 的位置。


![](http://img.lypeng.com/wiki/git/15.gif)

我们可以看到最新的动作已被推送给 reflog。
