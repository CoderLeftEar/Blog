---
title: "Git笔记11-git refspec、远程标签与git gc"
date: 2019-08-14T21:35:48+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记11-git refspec、远程标签与git gc
---

git refspec: 将本地的分支推送到远程的那个分支上。

---
git push 完整写法：src本地分支名称，dest远程分支名称  
git push origin src:dest  
git pull 完整写法：src远程分支名称，dest本地分支名称  
git push origin src:dest  

---

## 一、git refspec

### 1、HEAD

HEAD: 指向当前分支所在的引用标志符
查看HEAD：git symbolic-ref HEAD  
修改HEAD：git symbolic-ref HEAD refs/heads/develop

### 2、ORIG_HEAD
ORIG_HEAD: 远程最新的commit_id

### 3、FETCH_HEAD
FETCH_HEAD: 拉取下来后，最新的commit_id


## 二、远程标签

查看标签：git show v1.0  

推送标签：git push origin v1.0
(批量)：git push origin v1.0 v2.0
未推送全部推送：git push origin --tags
完整写法：git push origin refs/tags/v1.0:refs/tags/v1.0

拉取所有：git pull
拉取一个：git fetch origin tag v7.0


删除标签远程(git pull本地却不能体现)：  
git push origin :refs/tags/v1.0
git push origin --delete tag v1.0

删除本地：  
git tag -d v1.0  


## 三、远程分支底层
删除远程不存在的分支，本地还存在的远程分支：  
git remote prune origin  

本地访问本地远程分支origin/master:  
git log origin/master  
git log remotes/origin/master  
git log refs/remotes/origin/master  


.git/refs/heads下存在所有本地分支：
```
$ ls .git/refs/heads  
develop   master   test

```

.git/refs/remotes下存在所有本地远程分支  
.git/refs/tags下存在所有本地所有标签  


## 四、git gc
git gc：会将.git/refs目录下的所有文件打包到.git/packed-refs中

