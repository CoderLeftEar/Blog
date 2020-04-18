---
title: "Git笔记10-Git远程分支、别名、gitk与git gui"
date: 2019-08-14T00:38:46+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记10-Git远程分支、别名、gitk与git gui
---

别名设置：  
- 内部命令  
> git config --global alias.br branch
- 外部命令(需要加!)  
> git config --global alias.ui '!gitk'

------------------------------

## 1、非master分支(develop test)推送：  

在非master向远程推送：  
``` 
Mr.Wang@Mr-Wang MINGW64 /e/Desk/desk/gitTest (develop)
$ git push   
fatal: The current branch develop has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin develop

```

执行`git push --set-upstream origin develop`(或 `git push -u origin develop`)  
```
$ git push --set-upstream origin develop
Total 0 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for 'develop' on GitHub by visiting:
remote:      https://github.com/Mr-WangG/gitTest/pull/new/develop
remote:
To github.com:Mr-WangG/gitTest.git
 * [new branch]      develop -> develop
Branch 'develop' set up to track remote branch 'develop' from 'origin'.

```

推送成功：  
```
$ git br -av
* develop                2c144d0 Merge branch 'master' of github.com:Mr-WangG/gitTest
  master                 2c144d0 Merge branch 'master' of github.com:Mr-WangG/gitTest
  test                   2c144d0 Merge branch 'master' of github.com:Mr-WangG/gitTest
  remotes/origin/develop 2c144d0 Merge branch 'master' of github.com:Mr-WangG/gitTest
  remotes/origin/master  2c144d0 Merge branch 'master' of github.com:Mr-WangG/gitTest

```

git pull会将远程的所有分支都pull下来，但本地不会有具体分支：  
```
$ git br -av
* master                 2c144d0 Merge branch 'master' of github.com:Mr-WangG/gitTes
  remotes/origin/HEAD    -> origin/master
  remotes/origin/develop 2c144d0 Merge branch 'master' of github.com:Mr-WangG/gitTes
  remotes/origin/master  2c144d0 Merge branch 'master' of github.com:Mr-WangG/gitTes

```

-----------------

将本地的远程分支在本地创建出来：  
git checkout -b `develop`(可rename) origin/develop  
git checkout --track origin/develop  

<br>

删除本地：git br -d develop
删除远程：  
- old: git push origin :develop  
- new: git push origin --delete develop

<br>

---
git push 完整写法：src本地分支名称，dest远程分支名称  
git push origin src:dest  
git pull 完整写法：src远程分支名称，dest本地分支名称  
git push origin src:dest  

---


<br>

将本地develop分支推送到远程develop2分支（没有自动创建），但以后push会报错：  
git push --set-upstream origin develop:develop2
```
$ git push
fatal: The upstream branch of your current branch does not match
the name of your current branch.  To push to the upstream branch
on the remote, use

    git push origin HEAD:develop2

To push to the branch of the same name on the remote, use

    git push origin HEAD

To choose either option permanently, see push.default in 'git help config'.

```

使用git push origin HEAD:develop2推送到develop2
使用git push origin HEAD(develop)推动到develop











