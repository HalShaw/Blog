---

title: 你真的会Git吗？记一些实用命令
date: 2020-01-04 22:10
tags: Git 命令
categories: Git

---

# 一、前言

前几天看到阮一峰老师博客上面分享了一篇[如何撤销 Git 操作？](http://www.ruanyifeng.com/blog/2019/12/git-undo.html)的文章，读完之后不禁感叹自己真是个弱鸡，扪心自问，自己真的会`Git`吗？日常工作中的常用的基本命令倒是会几个，但是真的遇到点问题又要搞好久，现场搜索解决方案，着实会耽误些时间，导致自己的开发效率被拉低。自我反思之后，发现还是自己学艺不精，还需多下功夫，`工欲善其事，必先利其器`。在开发工具的使用方面还得下些功夫，所以，在此记录一些非常有用的提高效率的`Git`命令。

# 二、几个概念

- `HEAD`: 就是指向当前版本的指针。
- `Index`: `Index`为暂存区，当你修改某一个文件时，这个文件一开始是`unstaged`状态，使用`git add`命令把它加入到`Index`暂存区后，该文件变成了`staged`状态。然后执行`git commit`后，`Index`里面的修改被提交到本地版本库。
- `working tree`: 当前的工作目录。

<!--more-->

# 三、具体命令

### 撤销`git add .`

```
git reset HEAD
```
上面命令用来撤销错误`add`的代码，比如我不小心执行了个`git add .`，把当前所有的代码都添加到了缓冲区，到了`stage`阶段，但是还没`commit`，就可以用上面的命令来撤销`add`。

### 撤销`git commit -m`

如果执行过了`commit`，突然发现有不想要提交的代码，那现在怎么办呢？这个时候可以执行下面命令来撤销`commit`：

```
git revert HEAD
```
上面命令以一次新的提交覆盖上一次的提交内容，不会改变错误提交之前的内容，但是亲测之后发现错误提交的内容会丢失掉。但是有很多时候我们只是想修改其中的部分错误，而不是丢弃整个修改。

所以可以使用如下方法恢复之前的修改内容并再次修改，首先使用`git reflog`查看提交的历史，提取所需版本的版本号，然后执行`git reset 428713b`，后面是具体需要恢复的版本号即可，然后再在其上修改并且重新提交。
```
$ git reflog
e894f7e (HEAD -> master) HEAD@{0}: revert: Revert "test git"
428713b HEAD@{1}: reset: moving to HEAD
428713b HEAD@{2}: commit: test git
7395b28 (origin/master) HEAD@{3}: commit: Add some conponents.
6b1503b HEAD@{4}: commit (merge): First commit
f00f39f HEAD@{5}: commit (initial): First commit
```

```
$ git reset 428713b
Unstaged changes after reset:
M       src/pages/Cart.vue
```
`git revert`还有两个参数：
- --no-edit：执行时不打开默认编辑器，使用默认生成的信息，这个参数可以节省每次关闭`vi`编辑器的时间，因为从我的个人经验来说，基本上每次打开编辑器都只是执行一个关闭命令，而没有再次更改其中的信息。
- --no-commit：只抵消暂存区和工作区的文件变化，不产生新的提交。

`git reset`的用法：

```
git reset [<mode>] [<commit>]
```

`git reset` 将当前分支的`HEAD`指向给定的版本，并根据模式决定是否修改`Index`和`working tree`。
常用的`<mode>`有三种，`--soft`，` --mixed`， `--hard`，默认是`--mixed`。
- `--soft` 只会重置HEAD到对应的版本，不会修改`Index`和`working tree`。
- ` --mixed` 会修改`Index`暂存区，上面的`$ git reset 428713b`命令就相当于`$ git reset --mixed 428713b`
- `--hard` 会让暂存区及工作区里面的文件都回到以前的状态，一般不太推荐使用，可能会导致某一次提交彻底消失，且用且珍惜。

执行`git reset`命令之后，如果想找回那些丢弃掉的提交，可以使用`git reflog`命令，这种做法有时效性，时间长了可能找不回来。

### 替换上次提交`git commit --amend -m`
如果某次`git commit`的信息填写错误，可以使用`git commit --amend -m`来替换上次的提交信息，不单单是替换提交信息，这个命令的原理是用一次新的提交来替换上一次提交，如果此时内容有修改的话，也会被提交上去。

```
$ git commit --amend -m 'test amend'
[master 1e5ed2d] test amend
 Date: Sun Jan 5 10:29:11 2020 +0800
 1 file changed, 1 insertion(+), 1 deletion(-)
```
执行上面命令之后，使用`git log`查看提交历史，发现最新的一次提交信息已经改变了：
```
$ git log
commit 1e5ed2d5bf99f7223efd13b726354e35c695fae6 (HEAD -> master)
Author: xiaohao <xiaohaogreat@qq.com>
Date:   Sun Jan 5 10:29:11 2020 +0800

    test amend
```

### 从暂存区撤销文件
如果不小心`git add .`把所有文件都加入到了暂存区，可以使用下面的命令来从暂存区删除不想要的文件。
```
git rm --cached [filename]
```

```
$ git rm --cached src/pages/Cart.vue
rm 'src/pages/Cart.vue'
```

```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    src/pages/Cart.vue

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        src/pages/Cart.vue
```

### 暂存管理 `git stash`

假如你正在某个分支上开发，但此时有比较紧急的任务需要在同一分支上进行修改，然而你又不想两边内容混在一起，此时这个命令就很有用了。

```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 4 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   src/pages/Cart.vue

no changes added to commit (use "git add" and/or "git commit -a")

$ git  stash
Saved working directory and index state WIP on master: 5f34131 test checkout

$ git status
On branch master
Your branch is ahead of 'origin/master' by 4 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean

```
`git stash`可以把你当前的工作区里的内容放在缓存区里面进行保管，执行了`git stash`之后，工作区就是干净的了，可以进行新的修改，而两次的修改就不至于混在一起了。

```
$ git stash list
stash@{0}: WIP on master: 5f34131 test checkout
```
`git stash list`可以查看当前缓存起来的列表。

```
$ git stash apply
On branch master
Your branch is ahead of 'origin/master' by 4 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   src/pages/Cart.vue

no changes added to commit (use "git add" and/or "git commit -a")
```
`git stash apply`可以恢复当前缓存起来的修改。

```
$ git stash drop
Dropped refs/stash@{0} (aba8ef350840e0bf63528eebc6e375b9f326afa1)

$ git stash list
// 没有内容
```
`git stash drop`可以删除当前缓存区内的暂存修改。


# 四、参考
[http://www.ruanyifeng.com/blog/2019/12/git-undo.html](http://www.ruanyifeng.com/blog/2019/12/git-undo.html)
[https://www.jianshu.com/p/c6927e80a01d](https://www.jianshu.com/p/c6927e80a01d)
