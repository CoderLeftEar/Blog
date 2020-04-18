---
title: "Git笔记03-.gitignore与分支"
date: 2019-08-08T17:06:01+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记03-.gitignore与分支  
---

## 一、.gitignore

> 空目录git自动忽略  

通配符：  
> *.xml

不忽略设置：  
> !a.b

忽略当前目录下的TODO  
> /test.txt

忽略多层目录下的TODO  
> /*/test.txt  
> /**/test.txt

注释：  
> /**/test.txt

忽略build目录下的所有文件：  
> mydir/


创建目录并进入：  
> mkdir mydir && cd mydir

为创建.gitignore就提交，创建.gitignore后需要使用此命令清除缓存

> git rm -r --cached .




## 二、分支

查看分支：  
> git branch  

创建：  
> git branch new _branch

切换：  
> git checkout master  
> git checkout -  

**<font color = #f32 size = 4>在分支中添加或修改数据后需要add、commit</font>**

删除分支：  
> 在新分支没有添加：git branch -d new_branch  
> 添加文件后，再执行：git branch -D new_branch  

创建并切换：  
> git checkout -b new_branch  

合并：  
> git merge new_branch  

最新一条log：  
> git branch -v  

---
---
---

HEAD指向当前分支  
master指向当前提交

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/git分支01.jpg)    

新建分支只是新建dev指针指向master那个提交  
HEAD指向dev  

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/git分支02.jpg)    

在dev分支中修改后并提交  
dev就会指向第四个，HEAD永远指向当前分支  

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/git分支03.jpg)  

dev分支被修改后，再进行merge，master指针就会`快进`，直接指向dev所指向的第四个(合并：master`快进`到dev所指向的提交，不管dev添加、减少数据，master都会指向dev，与dev一致)  

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/git分支04.jpg)  


<font color=#f32 size=5>合并冲突：</font>  
master -> test.txt:  
```  
hello  
aaaaa  
```
dev -> test.txt:  
```  
hello  
bbbbb  
```

> git merge dev
```
Auto-merging test.txt
CONFLICT (content): Merge conflict in test.txt
Automatic merge failed; fix conflicts and then commit the result.
```

> git status
```
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

        both modified:   test.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

> 再次执行git add test.txt，这里是告诉已解决冲突。  

> git status  
```
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:

        modified:   test.txt

```

> git commit  
```
[master 34c196b] Merge branch 'dev'
```

`冲突解决完成`










