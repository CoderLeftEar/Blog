---
title: "Git笔记13-Git Subtree"
date: 2019-08-17T09:08:49+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记13-Git Subtree  
---

git subtree: 

添加远程项目设置别名subtree-origin：  
> git reomte add subtree-origin git@github.com:Mr-WangG/git_subtree_child.git  

从subtree-origin的master中克隆项目，  
-squash：将所需要依赖的所有分支的所有提交合并为一个提交，这个提交再与当前subtree-origin分支合并又发生一次提交(--prefix=subtree / --prefix subtree / -P subtree)  
> git subtree add --prefix=subtree subtree-origin master (-squash)

从subtree-origin拉取到subtree中：  
> git subtree pull --prefix=subtree subtree-origin master

在git_subtree_parent中，使用***可将依赖的项目进行修改并push到git_subtree_parent和git_subtree_child中，直接进行修改：  
> git subtree push --prefix=subtree subtree-origin master

**出现一下错误：可以采用以下方法，能解决部分**  
```
error: failed to push some refs to 'git@github.com:Mr-WangG/git_subtree_child.git'  
hint:   
hint: 
***
```

```
git pull origin master  
git pull --rebase origin master  
git push --set-upstream origin master  
```

原因是远程与本地不一致导致的
使用git pull --rebase origin master: 意思是将远程更新到合并到本地中，--rebase作用是取消本地中刚刚的commit，并临时保存到`.git/rebase`中，当同步远程到本地之后，再将临时保存的合并到本地当中。

<br>

远程与本地不一致导致的
![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/subtree01.png)   

使用git pull --rebase origin master  
![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/subtree02.png)   



使用--squash出现冲突：由于使用--squash将子项目的所有提交pull到一个提交上，导致merge时，可能找不到merge是需要的三方（需要merge的两个提交和它们的共同祖先这三个）。  

不使用--squash出现冲突：一直追溯到底都找不到共同祖先。  

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/subtree-squash.jpg)  


