<!-- TOC depthFrom:2 -->

- [1. Git 简介](#1-git-简介)
        - [集中式VS分布式](#集中式vs分布式)
- [2. Git 基础](#2-git-基础)
    - [2.1 初始配置](#21-初始配置)
    - [2.2 创建版本库](#22-创建版本库)
    - [2.3 版本管理](#23-版本管理)
        - [撤销修改](#撤销修改)
    - [2.4 远程仓库](#24-远程仓库)
        - [添加远程库](#添加远程库)
        - [实现fork的项目与原项目同步](#实现fork的项目与原项目同步)
        - [从远程库克隆](#从远程库克隆)
    - [2.5 分支管理](#25-分支管理)
        - [分支策略](#分支策略)
        - [多人协作](#多人协作)
    - [2.6 标签管理](#26-标签管理)
- [3. Git 进阶](#3-git-进阶)
    - [3.1 忽略特殊文件](#31-忽略特殊文件)
    - [3.2 配置别名](#32-配置别名)
    - [3.3 使用Github](#33-使用github)
    - [3.4 使用码云](#34-使用码云)
    - [3.5 搭建Git服务器](#35-搭建git服务器)
- [4. 难点及疑问](#4-难点及疑问)
    - [4.1 Rebase](#41-rebase)
    - [4.2 pull request](#42-pull-request)
- [5. 参考资料](#5-参考资料)

<!-- /TOC -->
# Git 学习笔记

## 1. Git 简介

Git是目前世界上最先进的分布式版本控制系统

    Git的诞生故事

#### 集中式VS分布式

集中式版本控制系统，版本库是集中存放在中央服务器的。集中式版本控制系统最大的毛病就是必须联网才能工作。

分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库。无需联网，安全性高。

## 2. Git 基础

### 2.1 初始配置

```
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

### 2.2 创建版本库

初始化一个Git仓库，使用`git init`命令。

添加文件到Git仓库，分两步：
1. 使用命令`git add <file>`，注意，可反复多次使用，添加多个文件；
2. 使用命令`git commit -m <message>`，完成。

```
git init
git add readme.txt
git commit -m "wrote a readme file"
```

### 2.3 版本管理

要随时掌握工作区的状态，使用`git status`命令。

如果git status告诉你有文件被修改过，用`git diff`可以查看修改内容。

在Git中，用`HEAD`表示当前版本，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个^比较容易数不过来，所以写成`HEAD~100`。

* Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
* 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
* 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

Git是跟踪修改的，每次修改，如果不用git add到暂存区，那就不会加入到commit中。

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，就是让这个文件回到最近一次`git commi`t或`git add`时的状态。

用命令`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区

#### 撤销修改

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

```
git status
git diff readme.txt
git log
git log --pretty=oneline
git reset --hard HEAD^
git reflog
git checkout -- readme.txt
git reset HEAD readme.txt
git rm test.txt
```

### 2.4 远程仓库

实际情况往往是这样，找一台电脑充当服务器的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。

    掌握创建SSH Key，在GitHub上添加SSH Key。

为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

```
ssh-keygen -t rsa -C "youremail@example.com"
```

#### 添加远程库

先在Github上创建一个同名的空仓库，然后进行关联，再进行版本推送。

要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；

关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；

```
git remote add origin git@github.com:Tyno945/learn-git.git
git push -u origin master
```

#### 实现fork的项目与原项目同步

```
1. 查看本地项目的远程信息
git remote -v

2. 添加原项目远程分支
git remote add upstream https://github.com/Tyno945/learn-git.git

3. 从原项目拉取分支到本地
git pull upstream master

4. 将本地代码提交到自己主页的分支，即origin上
git push origin master
```

#### 从远程库克隆

先创建远程库，然后，从远程库克隆。要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。

```
git clone git@github.com:Tyno945/learn-git.git
```

### 2.5 分支管理

    掌握创建分支的本质是创建指针

Git鼓励大量使用分支：

查看分支：`git branch`

查看所有分支（包括本地和远程）：`git branch -a`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

删除远程分支：`git push origin --delete <BranchName>`

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

用`git log --graph`命令可以看到分支合并图。

#### 分支策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场。

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

#### 多人协作

当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的master分支。你的小伙伴要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令`git checkout -b dev origin/dev`创建本地dev分支

多人协作的工作模式通常是这样：

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；

2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；

3. 如果合并有冲突，则解决冲突，并在本地提交；

4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

```
git checkout -b dev
git branch dev
git checkout dev
git branch
git branch -a
git merge dev
git branch -d dev
git push origin --delete dev
git log --graph --pretty=oneline --abbrev-commit
git merge --no-ff -m "merge with no-ff" dev
git stash
git stash list
git stash pop
git stash apply stash@{0}
git stash drop stash@{0}
git branch -D feature-vulcan
git remote
git remote -v
git push origin dev
git checkout -b dev origin/dev
git pull
git branch --set-upstream-to=origin/dev dev
```

### 2.6 标签管理

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。

* 命令`git tag <tagname>`用于新建一个标签，默认为HEAD，也可以指定一个commit id；

* 命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息；

* 命令`git tag`可以查看所有标签。

* 命令`git push origin <tagname>`可以推送一个本地标签；

* 命令`git push origin --tags`可以推送全部未推送过的本地标签；

* 命令`git tag -d <tagname>`可以删除一个本地标签；

* 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

```
git tag v1.0
git tag
git tag v0.9 f52c633
git show v0.9
git tag -a v0.1 -m "version 0.1 released" 1094adb
git tag -d v0.1
git push origin v1.0
git push origin --tags
git push origin :refs/tags/v0.9
```

## 3. Git 进阶

```
git config --global color.ui true
```

### 3.1 忽略特殊文件

在Git工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

忽略文件的原则是：

忽略操作系统自动生成的文件，比如缩略图等；
忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

所有配置文件可以直接在线浏览：https://github.com/github/gitignore

### 3.2 配置别名

### 3.3 使用Github

    如何参与一个开源项目

在GitHub上，可以任意Fork开源仓库；

自己拥有Fork后的仓库的读写权限；

可以推送pull request给官方仓库来贡献代码。

### 3.4 使用码云

国内的Git托管服务——[码云](https://gitee.com/)

```
git remote rm origin
git remote add github git@github.com:michaelliao/learngit.git
git remote add gitee git@gitee.com:liaoxuefeng/learngit.git
```

### 3.5 搭建Git服务器

## 4. 难点及疑问

### 4.1 Rebase

rebase操作可以把本地未push的分叉提交历史整理成直线；

rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

```
git rebase
```

### 4.2 pull request

如何使用Github参与开源项目？

## 5. 参考资料

[《廖雪峰Git教程》](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

[Git官网](http://git-scm.com)