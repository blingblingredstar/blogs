---
title: '[Git入门]本地仓库'
date: 2019-08-25 19:46:52
categories: 学习笔记
tags: [饥人谷, FrontEndMasters, git]
---
## GitHub和git

本文为饥人谷[git入门课程](https://xiedaimala.com/courses/16644d89-6b17-4c2f-ac15-dabb994b7696#/common)与FrontEndMaster上[git in depth](https://frontendmasters.com/courses/git-in-depth/)的学习笔记。

最大的程序员社区，是在线的代码托管平台。要是用GitHub，首先要学会git

### git配置

```bash
git config --global user.name <username>
git config --global user.email <email>
git config --global push.default simple
git config --global core.quotepath false
git config --global core.editor "code --wait"
git config --global core.autocrlf input
```
<!-- more -->
### 常用命令

```bash
git init
git status -sb
git log
git add
git commit -v
git reset --hard <SHA1>
git reflog
git branch <branch name>
git checkout <branch name>
git merge <要合并的分支>
# 首先进入要保留的分支，再使用命令，自行编辑要保留的内容，删除标记即可
# 解决后git add <filename>; git commit(不需要参数)
# 可以使用git branch -d <branch name>删除已合并分支
```

## Git基础

### 什么是git

git是一个分布式版本控制系统(Distributed Version Control System)

### git是如何存储数据的

核心是使用键值对来存储数据

+ 值是数据
+ 键是数据的哈希值（使用SHA1对数据压缩）
+ 使用键来访问数据

git使用SHA1加密数据，给定一段数据，产生一个独特的40位16进制数，给定的数据相同，那么产生的值也应该相同。

### Git Bolb

Git将压缩过得数据存储在一个blob中，blob的头部存储原数据

8ab686...
| blob        | `<size>` |
| ----------- | -------- |
| \0          |
| Hello world |

底层使用`git hash-object`来生成blob

```bash
echo 'Hello, World!' | git hash-object --stdin
8ab686eafeb1f44702738c8b0f24f2567c36da6d

echo 'blob 14\0Hello, World!' | openssl sha1
8ab686eafeb1f44702738c8b0f24f2567c36da6d
```

可以看到使用两个命令生成的哈希值是相同的。

### git init

使用`git init`会在当前工作目录下创建一个`.git`目录，里面会存储所有的仓库(repository)相关文件

### Git Tree

blob只能存储数据，我们还需要存储数据所在的文件名及目录名。此时需要引入tree。

git使用tree来存储数据所在的文件名及目录结构。

tree包含以下内容

+ 指向blob的指针
+ 指向其他tree的指针

还包含以下原数据

+ 指针的类型（blob或者tree）
+ 文件名称或目录名称
+ 模式（执行文件、链接符号）

哈希值(ji1....)
| tree |        | `<size>`       |
| ---- | ------ | -------------- |
| \0   |
| blob | 哈希值 | hello.txt      |
| tree | 哈希值 | 目录名(folder) |

### Git Commits

commit指向一个tree，并且包含以下元数据

+ 作者和提交者
+ 数据
+ 消息
+ 父类提交（上一级或者更多）

哈希值
| commit  | size     |
| ------- | -------- |
| tree    | 哈希键值 |
| parent  | 哈希键值 |
| author  | 作者名   |
| message | 提交信息 |

tree是仓库的一个快照，指向仓库的所有文件及目录

commit是代码的快照，存储了项目制定时段的状态，是暂存区(staging area)到上一个提交中所有更改的记录。

### 为什么无法更改commit

+ 如果更改了commit的数据，git就会生成新的哈希值（根据数据不同生成独特的哈希值）
+ 就算其他内容不变，但是生成时间会改变（哈希值随同改变）

### references - 指向commits的指针

+ Tags
+ Branches
+ HEAD - 指向当前commit的指针

为什么在git中更改分支速度很快？

因为更改分支并不产生新的数据，只是改变指针的指向。

```bash
❯ git log --oneline
adf0e13 (HEAD -> master) Initial commit

❯ cat .git/refs/heads/master # /refs/heads/master contains which commit the branch points to
adf0e13658d9b4424efe03c3ac3281facf288c13

cat .git/HEAD
ref: refs/heads/master # HEAD is usually a pointer to the current branch
```

### Working Area, Staging Area, Repository

git共有三个区域，working area为工作区，就是当前目录里全部文件，包括没有被git跟踪管理的文件及目录。

staging area为暂存区，也成为cache或index，就是上次提交到下次提交内所有被git跟踪的并修改过得文件。可以使用`git add`将未被跟踪的文件从工作区添加到暂存区。

最后是repository，就是本地仓库，包含了全部的提交。

### Staging Area

+ 暂存区是git保存上次提交到下次提交前所有文件及更改信息的区域。
+ 可以将staging area视为上次提交的一个拷贝。

### 将文件移入/移出暂存区

+ 将文件添加到下一个提交中
  + `git add <file>`
+ 将文件从下一个提交中删除
  + `git rm <file>`
+ 将文件在下一个提交中重命名
  + `git mv <file>`

### git add -p

+ 以大块方式提交
+ 交互式地!
+ 如果在一次提交中做了太多的工作，它尤其有用

### 将文件从暂存区中移出

+ 并不是删除文件
+ 只是将他们从仓库中使用一个拷贝进行替换

+ 使用`add`将工作区文件添加到暂存区
+ 使用`commit`将暂存区文件提交到仓库
+ 使用`commit -a`直接将工作区文件提交到仓库（不推荐）
+ 使用`checkout`将暂存区文件移出到工作区
+ 使用`checkout branch/SHA`将仓库文件移动到工作区

### git stash

+ 保存为提交的工作内容
+ 相对一些危险操作来说比较保险

如果你在工作中切换分支，可能丢失正在进行的修改，此时可以使用git stash来保存从上次提交到现在的工作内容。

+ 保存修改
  + `git stash`
+ 列出修改
  + `git stash list`
+ 显示内容
  + `git stash show stash@{0}`
+ 应用上次缓存
  + `git stash apply`
+ 应用指定缓存
  + `git stash apply stash@{0}`
+ 保存未被git跟踪的文件
  + `git stash --include-untracked`
+ 保存全部文件（**包括被忽略（.gitignore中）的！**）
  + `git stash --all`
+ 给缓存命名
  + `git stash save "WIP: making progress on foo"`
+ 从缓存开始新建一个分支
  + `git stash branch <optional stash name>`
+ 从缓存提取指定文件
  + `git checkout <stash name> -- <filename>`
+ 将最后一个缓存删除并应用修改
  + `git stash pop`
  + 如果有合并冲突就不会删除
+ 删除最后一个缓存
  + `git stash drop stash@{n}`
+ 清空**所有**缓存
  + `git stash clear`

### References

references是指向提交（commits）的指针。总共有三种类型。

1. Tags & Annotated Tags
2. Branches
3. HEAD

### branch

+ 分支（branch）是一个指向指定提交（commit）的指针
+ 当有一个新的提交时，当前分支的指针会随之改变

### HEAD

HEAD是git存储当前所在分支的信息，更重要的是git通过HEAD保存下次提交的父类信息。

HEAD是一个指针：

+ 通常指向当前分支
+ 也可以指向一个具体的提交（detached HEAD）

HEAD在以下情况下更改

+ 在当前活动分支进行提交
+ 切换到一个新的分支

### lightweight tags

+ lightweight tags就是提交的一个指针
+ 当创建一个tag并且没有参数时，会捕捉HEAD中的值

```bash
❯ git checkout master
Switched to branch 'master'

❯ git tag my-first-commit
```

### annotated tags: git tag -a

+ 指向一个提交，并存储可选的一些信息
  + 作者，信息，日期

```bash
❯ git tag -a v1.0 -m "Version 1.0 of my blog"

❯ git tag
my-first-commit
v1.0

❯ git show v1.0
tag v1.0
Tagger: Nina Zakharenko <nina@nnja.io>
Date: Sun Sep 24 17:01:21 2017 -0700
Version 1.0 of my blog
```

+ 显示仓库中的所有tags
  + `git tag`
+ 显示仓库中所有tags，并显示他们所指向的提交
  + `git show-ref --tags`
+ 显示所有指向特定提交的tags
  + `git tag --points-at <commit>`
+ 查看tag和内容
  + `git show <tag-name>`

### branch与tag的区别

+ branch
  + 当前分支的指针会指向仓库的最新一次提交
+ tag
  + tag指向的提交并不会更改
  + 是一个提交的快照

### HEAD-less/detached HEAD

+ 有时候需要切换到一个具体的提交（或tag）而不是分支
+ git会移动HEAD指向那个提交
+ 这时切换到一个其他分支或者提交，HEAD的值会指向一个新的哈希值
+ 这是在这个分离状态下的提交没有指向的引用了

此时如何保存工作内容：

+ 创建一个新的分支指向最后一个在分离状态（detached state）的提交
  + `git branch <new-branch-name> <commit>`
+ 为什么是最后一个提交？
  + 因为其他提交会保存父类提交的信息

### dangling commits

+ 如果你不将最新一次提交指向一个新的分支，那么git将不再引用这些提交（dangling commits）
+ 最终，这些提交会被垃圾回收

### git merge --no-ff(fast forward)

+ 为了保存合并提交的历史，即使可能并没有必要
  + `git merge --no-ff <commit>`

### merge conflicts

+ 当尝试合并但文件有分歧的时候
+ git会停止合并直到冲突解决

### git rerere - Reuse Recorded Resolution

+ 使用此命令git会记录你如何解决冲突
+ 下一个冲突时，复用刚才的解决办法

### 合理的提交信息

+ 好的提交帮助展示代码库的历史
+ 可以在如下起到帮助
  + debugging & 找到问题
  + 创建发布信息
  + 代码回顾
  + 代码回滚
  + 通过issure或ticket组织代码

### git log

+ `git log`
  + 显示仓库历史的基本命令
+ `git log --since`
  + 网站反应很慢，昨天到现在改了什么？
  + `git log --since="yesterday"`
  + `git log --since="2 weeks ago"`
+ `git log --follow`
  + 显示被移动或者重命名的文件
  + `git log --name-status --follow -- <file>`
+ `git log --grep`
  + 显示符合正则表达式的提交信息
  + `git log -grep <regexp>`
  + 可与git的其他命令混合使用
  + `git log --grep=mail --author=nina --since=2.weeks`
+ `git log diff-filter`

### git show

+ 显示提交和内容
  + `git show <commit>`
+ 显示提交中更改的文件
  + `git show <commit> --stat`
+ 查看其他提交中的文件
  + `git show <commit>:<file>`

### diff

+ diff会显示以下更改：
  + 两个提交之间
  + 暂存区和工作区之间
  + 工作区当前内容

+ 没有进入暂存取得更改
  + `git diff`
+ 进入暂存区的更改
  + `git diff --staged`

+ "diff"分支
  + 显示已经被合并到主分支并且可被清理的分支
  + `git branch --merged master`

### git checkout

+ 重新存储工作区文件或切换分支

+ 当使用checkout切换分支是发生了什么？
  1. 将HEAD指向新的分支
  2. 复制提交快照到暂存区
  3. 使用分支内容更新工作区
+ 当使用`git checkout --file`发生了什么
  + 将暂存区的文件内容复制到工作区的文件上（覆盖工作区文件）
  + **注意**：这个操作会覆盖文件并且没有警告。
+ 当使用`git checkout <commit> -- file`发生了什么
  + 更新工作区状态到指定的提交
  + 使用暂存区内容更新工作区
  + **注意**：这个操作会覆盖文件并且没有警告。

### git clean

+ `git clean`会清空工作区未被git跟踪的文件
  + **注意，此操作不可恢复**
+ 使用`dry-run`标识来查看将被删除的文件
+ 使用`-f`标识来确认删除
+ 使用`-d`标识来清空目录

### git reset

+ reset会根据参数不同有各种不同行为
  + 添加路径
  + 没有路径
    + 默认，git会使用`git reset -mixed`
+ 给定一个提交
  + 会移动HEAD的指针，可选文件更改
+ 给定一个文件路径
  + 不会移动HEAD指针，修改文件

+ `git reset --soft HEAD~`
  + 会将HEAD移动到前一个提交
+ `git reset HEAD~`等同于`git reset --mixed HEAD~`
  + 将HEAD移动到上一个提交，并将暂存区更新为上一个提交的状态。
+ `git reset --hard HEAD~`
  + 移动HEAD到上一个提交，并复制提交中的文件到暂存区和工作区
+ `git reset <commit> -- <file>`
  + 移动提交文件到暂存区

### 使用ORIG_HEAD来重做git reset

+ 为了防止意料外的`git reset -`
+ git会保存HEAD的前一个值到一个变量，就是ORIG_HEAD
+ 可以使用以下命令恢复
  + `git reset ORIG_HEAD`

### git revert

+ git revert会创建一个新的提交，包括了与指定提交所不同的操作
+ 原来的提交还保存在仓库里
+ 提示
  + 在一个共享的仓库中使用revert重做提交
  + revert不会更改历史记录

### git amend

amend是一个简单地且快速的方法来用以更改上一个提交

### git rebase

rebase将会更改提交的父类

### git rebase的建议

+ 在使用rebase/fixup/squash/recorder之前
+ 创建一个当前分支的副本
  + `git branch my_branch_backup`
+ `git branch`会创建一个新的分支，并不会切换分支
+ 如果rebase执行完毕但是中间出现问题
+ `git reset my_branch_backup --hard`
+ 一切恢复正常
