---
title: "Git笔记07-git远程、GitHub和公私钥"
date: 2019-08-12T22:56:37+08:00
author: CoderLeftEar
tags: ["git"]
categories: ["git"]
draft: true
---

# Git笔记07-git远程、GitHub和公私钥
---

添加到远程，别名为origin的https://github.com/Mr-WangG/gitTest.git：  
git remote add origin https://github.com/Mr-WangG/gitTest.git  

将master推送到origin并建立联系：  
git push -u origin master

所有远程仓库的别名：  
git remote show

显示具体的远程仓库信息：  
git remote show origin  
```
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/Mr-WangG/gitTest.git
  Push  URL: https://github.com/Mr-WangG/gitTest.git
  HEAD branch: master
  Remote branch:
    master tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)

```



公钥、私钥：  
添加到远程，别名为origin：  
git remote add origin git@github.com:Mr-WangG/gitTest.git  
查看信息（将远程地址添加到本机）：  
git remote show origin  
```
$ git remote show origin
The authenticity of host 'github.com (52.74.223.119)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com,52.74.223.119' (RSA) to the list of known hosts.
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

```

生成公钥和私钥(默认路径：/c/Users/Mr.Wang/.ssh)：  
ssh-keygen  
```
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Mr.Wang/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/Mr.Wang/.ssh/id_rsa.
Your public key has been saved in /c/Users/Mr.Wang/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:yFABcLYnqGStXdpvsxWlPZQyjE6PsWbpiPSiv7SptBA Mr.Wang@Mr-Wang
The key's randomart image is:
+---[RSA 3072]----+
|  ..+.o.         |
|  .+ o  o   .    |
| o..+..+ + +     |
|o.o +=o.* B      |
|E. + .oBS+ o     |
| .. o *   . .    |
|.. + o = .       |
|o + + . +        |
| +o=.  .         |
+----[SHA256]-----+

```

再次查看信息：  
```
$ git remote show origin
* remote origin
  Fetch URL: git@github.com:Mr-WangG/gitTest.git
  Push  URL: git@github.com:Mr-WangG/gitTest.git
  HEAD branch: (unknown)

```

提交，`此错误是没有add、commit`：  
```
$ git push -u origin master
error: src refspec master does not match any
error: failed to push some refs to 'git@github.com:Mr-WangG/gitTest.git'

```

add、commit后提交：  
```
$ git push -u origin master
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 230 bytes | 230.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:Mr-WangG/gitTest.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.

```

