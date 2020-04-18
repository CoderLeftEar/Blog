---
title: "Git笔记04-分支进阶与版本回退"
date: 2019-08-09T10:46:32+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记04-分支进阶与版本回退
---


图形化显示：  
git log --graph  
简写：  
git log --graph --abbrev-commit  

---

add、commit一步完成：  
git commit -am "commit"

---
## merge快进和不使用快进
<font color="red" size=5>dev:</font>  
```
* commit d1c84073b5aea7baa64f95f05f9cf291850d3888 (HEAD -> dev)
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:40:06 2019 +0800
|
|     third
|
* commit 0d51d62d68daf5e5c2bc80e74097cc84f450aef6 (master)
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:39:00 2019 +0800
|
|     seconed
|
* commit 2cb4d2524b959234f1cd79e4015b9b8339937bdf
  Author: Mr-WangG <mr.wang214880676@gmail.com>
  Date:   Sat Aug 10 20:38:24 2019 +0800

      first

```

<font color="red" size=5>master:</font>  
```
* commit 0d51d62d68daf5e5c2bc80e74097cc84f450aef6 (HEAD -> master)
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:39:00 2019 +0800
|
|     seconed
|
* commit 2cb4d2524b959234f1cd79e4015b9b8339937bdf
  Author: Mr-WangG <mr.wang214880676@gmail.com>
  Date:   Sat Aug 10 20:38:24 2019 +0800

      first

```

- 使用fast-forward:将master指向dev，即`快进`指向dev多提交一步的位置。
```
* commit d1c84073b5aea7baa64f95f05f9cf291850d3888 (HEAD -> master, dev)
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:40:06 2019 +0800
|
|     third
|
* commit 0d51d62d68daf5e5c2bc80e74097cc84f450aef6
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:39:00 2019 +0800
|
|     seconed
|
* commit 2cb4d2524b959234f1cd79e4015b9b8339937bdf
  Author: Mr-WangG <mr.wang214880676@gmail.com>
  Date:   Sat Aug 10 20:38:24 2019 +0800

      first

```
- 不使用fast-forward(git merge –no-ff dev):类似于先使用`快进`git merge dev，先达到commit bef87a 这次提交的位置，然后在进行一次提交 commit 041b8d  

使用git merge –no-ff dev合并：
```
*   commit 041b8d11f659369cc2726512522b4e16618a4718 (HEAD -> master)
|\  Merge: d1c8407 bef87a0
| | Author: Mr-WangG <mr.wang214880676@gmail.com>
| | Date:   Sat Aug 10 20:53:28 2019 +0800
| |
| |     Merge branch 'dev'
| |
| * commit bef87a031c16ff34f3faebcec30b3fde91f38c6e (dev)
|/  Author: Mr-WangG <mr.wang214880676@gmail.com>
|   Date:   Sat Aug 10 20:48:30 2019 +0800
|
|       four
|
* commit d1c84073b5aea7baa64f95f05f9cf291850d3888
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:40:06 2019 +0800
|
|     third
|
```

dev:
```
* commit bef87a031c16ff34f3faebcec30b3fde91f38c6e (HEAD -> dev)
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:48:30 2019 +0800
|
|     four
|
* commit d1c84073b5aea7baa64f95f05f9cf291850d3888 (master)
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:40:06 2019 +0800
|
|     third
|
* commit 0d51d62d68daf5e5c2bc80e74097cc84f450aef6
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:39:00 2019 +0800
|
|     seconed
|
* commit 2cb4d2524b959234f1cd79e4015b9b8339937bdf
  Author: Mr-WangG <mr.wang214880676@gmail.com>
  Date:   Sat Aug 10 20:38:24 2019 +0800

      first

```
master:
```
* commit d1c84073b5aea7baa64f95f05f9cf291850d3888 (HEAD -> master)
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:40:06 2019 +0800
|
|     third
|
* commit 0d51d62d68daf5e5c2bc80e74097cc84f450aef6
| Author: Mr-WangG <mr.wang214880676@gmail.com>
| Date:   Sat Aug 10 20:39:00 2019 +0800
|
|     seconed
|
* commit 2cb4d2524b959234f1cd79e4015b9b8339937bdf
  Author: Mr-WangG <mr.wang214880676@gmail.com>
  Date:   Sat Aug 10 20:38:24 2019 +0800

      first

```

---

## 版本回退

可加n个^:hard前是双-
> git reset --hard HEAD^^可加n个^  

后面数字可写n:
> git reset --hard HEAD~2

回退、前进到任意版本：  
> git reset --hard `34c1` ---> commit前几位










