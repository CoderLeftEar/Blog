---
title: "Git笔记14-git cherry-pick"
date: 2019-08-17T18:37:56+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记14-git cherry-pick

## git cherry-pick

git cherry-pick：错误的在dev分支比master分支比多两个提交  

```
master:  
commit 04f05f3a7076f38ccacb5cd544727d108599ff39 (HEAD -> master)
Author: zhangsan <zhangsan@git.com>
Date:   Sun Aug 18 22:41:08 2019 +0800

    initial commit

==============================================  

dev:  
commit a84738c41b782e33e00fd6491e0e7e523bd18c97 (HEAD -> dev)
Author: zhangsan <zhangsan@git.com>
Date:   Sun Aug 18 22:42:35 2019 +0800

    add hello3

commit c5ea82c4dde3277efa9b4640131681f0ce7f1f04
Author: zhangsan <zhangsan@git.com>
Date:   Sun Aug 18 22:42:03 2019 +0800

    add hello2

commit 04f05f3a7076f38ccacb5cd544727d108599ff39 (master)
Author: zhangsan <zhangsan@git.com>
Date:   Sun Aug 18 22:41:08 2019 +0800

    initial commit

```

在master上使用`git cherry-pick c5ea82`可将在dev分支上提交到的所有信息在当前master上再次执行，但不能跳过执行，不能直接执行`git cherry-pick a84738`，会报冲突错误，需手动排除冲突  

将dev上的提交都在master上执行完毕后  
- 使用`git checkout 04f05f`回到第一次提交上，删除dev分值后，重新创建。
- 使用`git reset –hard HEAD^^`进行两次版本回退。
















