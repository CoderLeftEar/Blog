---
title: "Git Stash删除后找回"
date: 2020-10-15T16:52:25+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---



# Git Stash删除后找回

## 查询所有记录

```
git log --graph --oneline --decorate  $(git fsck --no-reflog | awk '/dangling commit/ {print $3}')
```

## 添加到工作区

```
git stash apply 9fa8ujaf
```



