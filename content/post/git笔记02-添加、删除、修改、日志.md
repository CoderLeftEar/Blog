---
title: "Git笔记02-添加、删除、修改、日志"
date: 2019-08-08T11:22:05+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记02-添加、删除、修改、日志
---

## 〇、git add的三个作用：  
- 刚创建的未追踪的文件加入到追踪列表当中  
- 将已经追踪并修改的文件纳入到暂存区（缓存区）  
- 文件出现冲突，解决冲突后，add告知冲突已被解决  



## 一、删除命令

### 1、git rm file

- 删除了一个文件(工作区)
- 将被删除的文件纳入到暂存区中

执行提交就完成删除操作了，但是不能恢复

删除后恢复文件：

- git reset HEAD file --> git checkout -- file,完成文件恢复

- git reset HEAD file --> git add file,可看做将删除的文件提交到暂存区（commit后彻底删除），也可重复执行上一步^^^^^。

### 1、rm file

- 删除了一个文件(工作区)

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    test2.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

可执行相应的操作完成文件的删除和操作。

删除暂存区：git rm --catch

## 二、重命名

### **1、git mv file**

重命名：创建一个新文件，内容和源文件一样，再删除源文件，并提交到暂存区。

- 创建与源文件内容相同的新文件 --- 删除源文件

> $ git mv test.txt test2.txt

#### 1.成功重命名

```
git mv test.txt test2.txt  
git commit -m "commit"
```

#### 2.取消重命名

```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        renamed:    test.txt -> test2.txt

```

源文件删除后 --- add 暂存区  
使用git reset HEAD test.txt 回退到工作区

```
$ git reset HEAD test.txt

$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   test2.txt

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    test.txt

```

使用git reset HEAD test2.txt 使新文件从暂存区退回到工作区

```
$ git reset HEAD test2.txt

$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    test.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test2.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

但此时工作区还是只有重命名（新文件）的文件。

```
$ ls
test2.txt
```

使用 git checkout -- test.txt 将源文件回退到更改后（删除后）的前一步，test.txt 的状态是存在的（即将被删除）。

```
$ git checkout -- test.txt

$ ls
test.txt  test2.txt
```

此时新文件将相当于刚创建，等待add

```
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test2.txt

nothing added to commit but untracked files present (use "git add" to track)

```

删除新文件

```
$ rm test2.txt

$ git status
On branch master
nothing to commit, working tree clean

```

此时只剩源文件，内容没发生改变

```
$ cat test.txt
welcome
hello

$ ls
test.txt

```

### **2、mv file**

没有提交到暂存区

```
$ mv test2.txt test.txt

$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    test2.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

再次执行add手动提交，后面的commit和取消rename与git mv file相同

```
$ git add test.txt test2.txt

Mr.Wang@Mr-Wang MINGW64 /e/Desk/desk/gitTest (master)
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        renamed:    test.txt -> test2.txt

```

## 三、git log

修改上一次的提交说明  
> git commit --amend -m "修改后的提交"


查看最近的n条log：  
> git log -n

一行显示：  
> git log --pretty=oneline

```
ae340db2499baee9058ffae18f2af9e4597919aa (HEAD -> master) something content  
16c4042d84ccb23ad1a83e6e155419266288ad60 something  
dee493599fe8f09b26cc2bee4eeb9435e07f295a remove test2  
```

详细显示：  
> git log --pretty=format:"%h - %an, %ar : %s"

```
ae340db - Mr-WangG, 7 minutes ago : something content  
16c4042 - Mr-WangG, 8 minutes ago : something  
dee4935 - Mr-WangG, 11 minutes ago : remove test2  
```

`git log --graph`

帮助文档  
> git help config  
> git config --help

