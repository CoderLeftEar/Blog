---
title: "Git笔记06-标签与diff"
date: 2019-08-11T18:09:07+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记06-标签与diff
---

标签：不区别分支  
git tag v1.0  
git tag -a v2.0 -m "2.0 released"


查看：  
git tag  
git show v1.0
精确查找：  
git tag -l "v2.0"  
模糊查找：  
git tag -l "*2"  


删除：  
git tag -d v1.0  

---

文件上一次修改人的信息：  
git blame  

---

Linux中的diff：  
diff a b  
diff -u a b  

git中的diff：  

1.  
暂存区与工作区的区别(原始文件：暂存区  目标文件：工作区)：  
git diff  

```
$ git diff
warning: LF will be replaced by CRLF in test.txt.
The file will have its original line endings in your working directory
diff --git a/test.txt b/test.txt
index 94954ab..d56682a 100644
--- a/test.txt  ---> `暂存区`
+++ b/test.txt  ---> `工作区`
@@ -1,2 +1,3 @@
 hello
 world
+welcome

```

2.  
最新的提交与工作区的差别：  
git diff HEAD
git diff commit_id  

```
$ git diff HEAD
diff --git a/test.txt b/test.txt
index 94954ab..d56682a 100644
--- a/test.txt  ---> `版本库`
+++ b/test.txt  ---> `工作区`
@@ -1,2 +1,3 @@
 hello
 world
+welcome

```

3.  
最新的提交与暂存区的区别：  
git diff --cached (commit_id)

```
$ git diff --cached
diff --git a/test.txt b/test.txt
index 94954ab..d56682a 100644
--- a/test.txt  ---> `版本库`
+++ b/test.txt  ---> `暂存区`
@@ -1,2 +1,3 @@
 hello
 world
+welcome

```