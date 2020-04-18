---
title: "Git笔记01-基本命令操作"
date: 2019-08-06T00:11:45+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记01-基本命令操作
---

## **一、基本命令**

### 1、git init:初始化本地仓库

### 2、git status:当前处于什么状态

```
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test.txt

nothing added to commit but untracked files present (use "git add" to track)

```

### 3、git add .:将文件纳入暂存区当中

`git add .`：添加新增文件和修改的文件

`git add -u`（git add -update）：只添加修改的文件

`git add -A`：添加所有文件，包括删除的文件

```
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   test.txt


```

### 4、git rm --cached file:将已提交的文件从暂存区删除，回到未提交到暂存区的状态

git rm --cached file:会删除暂存区和工作区的文件

```
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test.txt

nothing added to commit but untracked files present (use "git add" to track)

```

### 5、git commit -m "commit":将文件提交到git的版本库

-m:message

```
$ git commit -m "first commit"
[master (root-commit) a4bec48] first commit
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt

```

### 6、git log:查看提交历史

### 7、使用vim、echo更改文件后，使用git status

```
vim test.txt
echo "welcome" > test.txt
```

test.txt文件修改后：  
 - git checkout -- file 回退到文件数据更改前（取消对文件的更改操作）

 - git reset HEAD file 由暂存区回到工作区，git checkout -- file 回退到文件数据更改前（取消对文件的更改操作）

没有使用git add .提交到暂存区，就使用git checkout -- file ，就永远改不能复原了，因为文件没有add、commit，纳入版本库。


## **二、设置用户,可在以下位置修改**

### 1、/etc/gitconfig

查看：  
> git config --list

```
git config --system user.name "username"
git config --system user.email "email@email.com"

```

### 2、/users/Mr.Wang/.gitconfig


```
git config --global user.name "username"
git config --global user.email "email@email.com"

```

### 3、.git/config

```
git config --local user.name "username"
git config --local user.email "email@email.com"

删除用户信息：
git config --local --unset user.name

```

### 4、修改初始化后提交的user.name和user.email信息：  
> git commit --amend --reset-author
