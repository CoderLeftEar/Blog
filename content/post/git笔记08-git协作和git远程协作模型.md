---
title: "Git笔记08-Git协作和Git远程协作模型"
date: 2019-08-13T09:49:31+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记08-Git协作和Git远程协作模型
---

## 第一部分  

查看所有分支：  
```
$ git branch -a
* master
  remotes/origin/master
```

查看所有远程分支：  
```
$ git branch -r
  origin/master
```

查看所有分支最后一次提交：  
```
$ git branch -av
* master                01c84c4 commit
  remotes/origin/master 01c84c4 commit
```

再进行一次提交后，执行git branch -av，master比origin/master多一次提交：  
```
$ git branch -av
* master                9794ba4 [ahead 1] commit
  remotes/origin/master 01c84c4 commit

```

`执行git push，执行了两件事，将master推送到远程，origin/master的当前提交指向master的提交。`



## 第二部分

执行git pull将本地从远程Updating 9794ba4..906f1df（快进）：  
```
$ git pull
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:Mr-WangG/gitTest
   9794ba4..906f1df  master     -> origin/master
Updating 9794ba4..906f1df
Fast-forward
 test.txt | 1 +
 1 file changed, 1 insertion(+)

```

<font color=red size=6>凡事先拉后推</font>














