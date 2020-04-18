---
title: "Git笔记12-Git裸库与submodule"
date: 2019-08-15T01:48:11+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记12-Git裸库与submodule
---

## git裸库  

没有工作区的工作仓库

mkdir git-bare && cd git_bare
git init -bare

## submodule

将依赖的项目添加到本项目中：  
> git submodule add git@github.com:Mr-WangG/git_child.git mymodule

进入mymodule目录中使用git pull更新来过来的单个项目  
将所有依赖的项目更新：git submodule foreach git pull  

**clone**
- 克隆父项目不会将依赖的项目clone，mymodule目录中是空的，需要手动clone
git clone git@github.com:Mr-WangG/git_parent.git git_par
git submodule init  
git submodule update --recursive  

<br>

- 克隆父项目时，会将依赖的项目一起克隆下来
git clone git@github.com:Mr-WangG/git_parent.git git_par --recursive

**delete**
将mymodule从暂存区删除：  
git rm --cached mymodule  
将mymodule从工作区删除：  
rm -rf mymodule  
git add .
git commit -m "remove submodule"  
git push  
mymodule文件已经不存在了，将.gitmodule文件删除




