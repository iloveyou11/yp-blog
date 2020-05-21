---
title: git基础
date: 2020-04-07
categories: 技能
author: yangpei
tags:
  - git
comments: true
cover_picture: /images/banner.jpg
---

对于git做深入地了解，总结了在实际工作过程中git进行版本管理和团队协作的基本操作方法。

<!-- more -->

### 一、介绍
git是什么？git是一款分布式版本控制软件。

**三种状态**
  现在请注意，如果你希望后面的学习更顺利，请记住下面这些关于 Git 的概念。 Git 有三种状态，你的文件可能处于其中之一： 已提交（committed）、已修改（modified） 和 已暂存（staged）。
1. 已修改表示修改了文件，但还没保存到数据库中。
2. 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。
3. 已提交表示数据已经安全地保存在本地数据库中。

**三大区域：**
1. 工作区
2. 暂存区
3. 版本库

<img width="100%" src="https://i.loli.net/2020/04/07/tUqNBwiuPGcDCRX.jpg" alt="git" />

**基本的 Git 工作流程如下：**
1. 在工作区中修改文件。
2. 将你想要下次提交的更改选择性地暂存，这样只会将更改的部分添加到暂存区。
3. 提交更新，找到暂存区的文件，将快照永久性存储到 Git 目录。

如果 Git 目录中保存着特定版本的文件，就属于 `已提交` 状态。 如果文件已修改并放入暂存区，就属于 `已暂存` 状态。 如果自上次检出后，作了修改但还没有放到暂存区域，就是 `已修改` 状态。

安装 Git 后，初次运行 Git 前的配置：
```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

**检查配置信息:**
如果想要检查你的配置，可以使用 git config --list 命令来列出所有 Git 当时能找到的配置。

### 二、实战
1、在家里上传代码
```
git remote add origin [github@git]
git push -u origin
```
2、到公司新电脑上第一次获取代码
```
git clone [github@git]
git checkout [branch]
```
3、在公司进行开发
```
git checkout dev 切换到分支进行开发
git merge master 把master分支合并到dev【仅一次】

git add .
git commit -m "xx"
git push origin dev
```
4、回家后
```
git checkout dev
git pull origin dev

git add .
git commit -m "xx"
git push origin dev
```
5、开发完毕，上线
```
1）将dev分支合并到master并推送到远程
git checkout master # 切换到master分支
git merge dev # 合并dev分支
git push origin master # 将当前master的代码推送到远程master分支

2）把dev分支也推送到远程
git checkout dev # 切换回dev分支
git merge master # 这里保证了dev与master分支相同
git push origin dev # 将dev分支推送到远程
```

**git rebase（变基，使git提交记录更简洁）应用场景：**
1、帮助你将你的多个提交记录整合成一个提交记录
```
git rebase -i [commitID] 将现在提交版本号和commitID提交版本号中间所有的提交做合并
git rebase -i HEAD=3 将最近的3条记录做合并（s代表合并到上个版本提交）
```
【注意】如果某条记录已经提交到远程仓库了，最好不要把它也做上rebase合并，尽量合并尚未提交到远程仓库的记录。尽量不要在已push至远程库后在使用变基操作。

2、master分支在向前开发，dev分支也在向前开发，如果要merge到master中时，会产生以下的记录：

<img width="60%" src="https://i.loli.net/2020/04/07/w2YBGI7OArlQgv6.jpg" alt="rebase场景2" />

如果想要简化记录，使C3插入到C2和C4中间（归并到一条记录，减少分叉log记录）
```
git checkout dev
git rebase master
git checkout master
git merge dev
```

3、在公司写代码忘记了推送到远程，回家后接着开发其他功能并推送至远程，回公司后需要拉取代码并做合并，这种情况下会产生log记录分叉（使用git pull直接拉取代码的话）。这时，使用`git fetch origin dev`+`git rebase origin/dev`可以解决，不会再产生分叉记录。

**注意事项：**`git rebase`可能会产生冲突，则需要解决冲突，再往下进行

**总结**
1、添加远程连接
`git remote add origin [git地址]`
2、推送代码
`git push origin dev`
3、下载代码
`git clone`
4、拉取代码
`git pull origin dev`
等价于`git fetch origin dev`+`git merge origin/dev`
5、变基（保持代码提交简洁）
`git rebase [分支]`
6、记录图形展示
`git log --graph --pretty=format:"%h %s"`

### 三、协同开发gitflow工作流

<img width="100%" src="https://i.loli.net/2020/04/07/WCM8znSdlYiOZPH.jpg" alt="git工作流思路" />

1. 现有master分支V1版本（master分支只放公司线上正在运行的代码）
2. 创建分支dev，并拆分出所有的子功能分支（A、B、C……）
3. A子分支功能开发完成后，进行code review后合并到dev分支（使用pull request），其他子功能分支开发完同理。子功能开发完成后对应的分支可予以删除。
4. 创建release分支专门做测试，测试dev分支上的代码，发现bug要进行修复，修复完成后提交回dev分支，无bug后才能合并至master上线
5. 如果在各子功能都在开发的过程中，线上出现紧急bug，应该额外创建bug分支，修复完bug后合并回master

### 四、命令汇总
**1. 获取 Git 仓库**
通常有两种获取 Git 项目仓库的方式：
- 将尚未进行版本控制的本地目录转换为 Git 仓库；
  
```
  $ git init
  $ git add *.c
  $ git add LICENSE
  $ git commit -m 'initial project version'
```
- 从其它服务器 克隆 一个已存在的 Git 仓库。

```
  $ git clone https://github.com/libgit2/libgit2
  $ git clone https://github.com/libgit2/libgit2 mylibgit
```

**常见操作：**
```
  git init | 仓库的初始化  
  git clone [url] | 从某个远程仓库拷贝  
  git status | 显示工作目录的状态
  git add [file1] [file2] [file3] | 跟踪新文件、已修改的文件添加到暂存区
  git commit -m [msg]| 提交文件 
  git rm [file] | 将已跟踪的文件从工作目录、暂存区移除，注意是已跟踪的
  git mv | 将已跟踪的文件重新命名，或者将文件从一个目录移动到另一个目录
  git diff  --cached | 暂存区与仓库之间的差异
  git diff [file] | 查看指定的文件差异
  git diff [commitId_1] [commitId_2] | 查看2个指定版本的差异
  git log (--graph)  | 查看历史记录 
  git reflog | 查看历史提交记录，包括你没有更新的提交
  git reset --hard   HEAD~ |  将本地仓库、暂存区、工作目录恢复到上一个版本（所有的修改将会失去）
  git reset --mixed HEAD~ | 将本地仓库、暂存区恢复到上一个版本，工作目录保存着修改
  git reset --soft HEAD~ | 将本地仓库、上一个版本，暂存区、工作目录保存着修改
  git reset HEAD~2 [path] | 带文件路径，默认是--mixed，只将暂存区，路径path下的文件恢复到之前2个版本
  git checkout [file] | 撤销工作区中已修改的文件
  git commit  --amend | 覆盖上一次的提交。
```

**远程仓库相关命令：**
```
git clone <url>   克隆远程仓库到本地
git remote  列出每个远程仓库的简短名字
git remote -v    列出每个远程仓库的简短名字与其对应的 URL
git remote show [remote-name]   查看某个远程仓库的详细信息
git remote rename [old name] [new name]  重命名远程仓库
git remote rm [remote-name]   移除某个远程仓库
git remote add <shortname> <url>  添加一个远程仓库
git fetch [remote-name]  从远程仓库数据拉取最新到本地，但不自动合并本地的修改
git  pull   [remote-name] [branch-name]  把远程仓库数据拉到本地，并自行合并
git pull 的魔法经常令人困惑所以通常单独显式地使用 fetch 与 merge 命令会更好一些。
git  push [remote-name] [branch-name]    把本地代码推送到远程仓库，一般先执行git pull、在执行git push  确
```

**分支相关命令：**
```
git branch   查看分支（当前工作分支前面会标一个*号）
git branch -v  查看每一个分支的最后一次提交
git branch -vv  查看每一个分支的详细信息，如每一个分支正在跟踪哪个远程分支与本地分支是否是领先、落后
git show-branch   详细查看的分支记录

git  branch <branchname>       创建分支， HEAD 的特殊指针也会移到当前分支
git checkout  <branchname>   切换分支
git checkout  -b <branchname> 创建分支，并切换到该分支，即合并上面2步  

git mergr  <branchname> ：合并分支，如果需要合并到master分支，那么需要先切换到master分支，再进行整合 (该合并分支，是Fast forward模式，在服务器中是没有记录的)
git merge --no-ff -m "merge with no-ff" <branchname>     合并分支（禁用Fast forward模式，能看到分支记录）

git branch --merged   查看已经合并到当前分支的分支。 
git branch --no-merged  查看尚未合并到当前分支的分支。 
git branch -d  <branchname>        删除已经合并的分支
git branch -D  <branchname>      可强制删除尚未合并的分支 
git push origin --delete serverfix    删除某个远程分支

git checkout -m <branchname>  将本地的修改加入到新的分支上

git checkout -b branch-name  origin/branch-name 在本地创建和远程分支对应的分支，本地和远程分支的名称最好一致
```

### 五、其他
**给开源软件贡献代码：**
1、fork源代码（拷贝源代码到自己的仓库）
2、在自己的仓库修改代码
3、给源代码作者提交修复bug的申请（pull request）

**免密登录：**

<img width="60%" src="https://i.loli.net/2020/04/07/soaVlvdKjBwp3yT.jpg" alt="git免密登录" />

**任务管理相关：**
1. issues（文档、big修复等任务管理）
2. wiki（写项目介绍）