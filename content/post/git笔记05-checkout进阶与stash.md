---
title: "Git笔记05-checkout进阶与stash"
date: 2019-08-10T16:02:24+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记05-checkout进阶与stash  
---

## checkout  

创建并切换到3fs345（临时名称、分支）提交的分支：  
> git checkout 3fs345



创建mycommit分支并指向3fs345：  
> git branch mycommit 3fs345


分支重命名：  
> git branch -m master master2

---

## stash  

临时存储：
> git stash  

添加说明：  
> git stash save "commit"  



查看：`stash@{0}表示最新`  
> git stash list  


恢复并删除当前stash：  
> git stash pop  


恢复不删除当前stash：  
> git stash apply  

手动删除：  
> git stash drop stash@{0}


指定恢复之前stash的内容：  
> git stash apply stash@{0}

---






