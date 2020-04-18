---
title: "Git笔记09-Git分支最佳实践"
date: 2019-08-13T22:01:17+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记09-Git分支最佳实践
---

git add的三个作用：  
- 刚创建的未追踪的文件加入到追踪列表当中  
- 将已经追踪并修改的文件纳入到暂存区（缓存区）  
- 文件出现冲突，解决冲突后，add告知冲突已被解决  


vim编辑下：  
- dd删除一行
- 2,4d: 删除2到3行

----------------------------------


## 一、**修改冲突问题：**  
第一个用户修改commit，push后，第二个用户再次修改commit，push：
```
$ git push
To github.com:Mr-WangG/gitTest.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:Mr-WangG/gitTest.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

```

第二个用户pull，更新本地仓库：  
```
$ git pull
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:Mr-WangG/gitTest
   906f1df..3028a25  master     -> origin/master
CONFLICT (add/add): Merge conflict in hello.txt
Auto-merging hello.txt
Automatic merge failed; fix conflicts and then commit the result.

```

查看冲突文件，并解决冲突后，查看git status：  
```
$ cat hello.txt
<<<<<<< HEAD
hello2
=======
hello1
>>>>>>> 3028a252a5e3806c52c1b91c5ce90056f50f9327

```
```
$ git status
On branch master
Your branch and 'origin/master' have diverged,
and have 1 and 1 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

        both added:      hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

使用git add命令，告知冲突已被解决，再执行git commit，此时两个用户会进行merge，此时用户2会比origin/master多出两个提交点`({`  
    &ensp;&ensp;&ensp;&ensp;**1、未commit：**  
    &ensp;&ensp;&ensp;&ensp;用户1：  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;commit `3028a252a5e3806c52c1b91c5ce90056f50f9327 (HEAD -> master, origin/master)`  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;commit 906f1dffe9ed71ad5ecdae2e694ddf97769a6145  
    &ensp;&ensp;&ensp;&ensp;用户2：  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;commit `7bf31a05caf845aa27abc90de379e14bfe98a443 (HEAD -> master)`  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;commit 906f1dffe9ed71ad5ecdae2e694ddf97769a6145     
    &ensp;&ensp;&ensp;&ensp;**2、commit后：**  
    &ensp;&ensp;&ensp;&ensp;用户1：  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;commit `3028a252a5e3806c52c1b91c5ce90056f50f9327 (HEAD -> master, origin/master)`  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;commit 906f1dffe9ed71ad5ecdae2e694ddf97769a6145  
    &ensp;&ensp;&ensp;&ensp;用户2：  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`commit 2c144d0b2da449eeb2d4ef549bf6678efa11cbdb (HEAD -> master)`  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`commit 7bf31a05caf845aa27abc90de379e14bfe98a443`  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`commit 3028a252a5e3806c52c1b91c5ce90056f50f9327 (origin/master, origin/HEAD)`  
    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;commit 906f1dffe9ed71ad5ecdae2e694ddf97769a6145

`})`：  
```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean

```

最后git push提交到远程

----------

## 二、杂

git pull = git fetch + git merge

git分支：  
- master：生产  
- test：测试  
- develop：变化最频繁  
- hotfix(bugfix)：出问题，紧急修复  











