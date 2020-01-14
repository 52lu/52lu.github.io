---
title: Git-相关的问题
date: 2016-11-23
tags:
 - git
categories:
 - 入坑历史
---

# Q1 解决git pull/push每次都需要输入密码问题

解决办法:
- 进入你的项目目录，输入：
- > git config \--global credential.helper store

# Q2 git无法pull仓库refusing to merge unrelated histories
解决方法: 
```
git pull origin master --allow-unrelated-histories
```

# Q3  error: cannot lock ref refs/remotes/origin..

报错:
```
error: cannot lock ref 'refs/remotes/origin/分支名': ref refs/remotes/origin/分支名... 
is at 19070aed6873f8d58f35e4631272b59f13927a1c but expected 8a5b3bda0778070bd6b92123556475c9484e04b8
```

解决方法:
``` 
# rm .git/refs/remotes/origin/分支名
# git fetch
```

# Q4 warning: 远程 HEAD 指向一个不存在的引用，无法检出

解决方法： 
```
原因是.git目录下.git/refs/heads不存在HEAD指向的文件，这个时候可以用git show-ref命令查看 
获得如下打印： 
5fa0b60252ca2c10fa3c2e12780d351c047c802d refs/remotes/origin/branch_qc_origin 
5fa0b60252ca2c10fa3c2e12780d351c047c802d refs/tags/XXXXXXXXXXXX 
48dee3a5f7b9cac98349e949275c652e02b0b67e refs/tags/PXXXXV0.0.0B01-bringup 
505a9bb0c6d815e6db561f7cb7ed0e20cd73ddde refs/tags/PXXXX_02550_201309260427 
e2b211a3c9d8dabec9fe1018b4f7db5c953832ea refs/tags/PXXXX_bsp_02550_201309260422 
0848e8b7922c78dbb364aa0e7c1b8375d16a70a3 refs/tags/branch_PXXXX_02550_201309190102 
7384803be7c8866393b96ebaaa7e1b2b119654e7 refs/tags/branch_PXXXX_02550_201309200102 
480f444dd26bb238aeaacf15da748ad861ea9378 refs/tags/branch_PXXXX_02550_201309210102 
…… 
可以看出，全部是标签tag，并没有类似refs/head/branch_name 
继续执行命令： 
git branch //输入出空 
git branch -a //输出 remotes/origin/branch_qc_origin 
git checkout remotes/origin/branch_qc_origin // ① checkout的是git branch -a输出的内容 
这样通过ll命令查看，Contacts代码下载到工作目录了 
接着创建分支： 
git checkout -b remotes/origin/branch_qc_origin // ② 创建分支 
git branch //可以看到输出*remotes/origin/branch_qc_origin了，不再为空 
git branch -m remotes/origin/branch_qc_origin master // ③ 重命名分支叫master 
git show-ref命令查看也能看到head了 
至此，问题得到解决。

```


# Q5 gitLab:[remote rejected] master -> master (pre-receive hook declined)

原因:
 1. 项目中对应的分支，设置成了已保护，无法push
 2. ssh克隆下的地址有问题，具体什么问题还不清楚，对应的方案使用http

报错:
```
remote: GitLab: You are not allowed to push code to protected branches on this project.
To ssh://git.xxx.com:22138/ms-group/mm-test.git
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'ssh://git@git.xxx.com:22138/ms-group/mm-test.git'
```

解决方法：
 - 第一步:查看gitlab中对应的项目是否设置保护分支;
 ![](https://52lu.github.io/directionsImg/git/gitlab-pre-receive-hook-declined.png)
 ![](https://52lu.github.io/directionsImg/git/gitlab-protected-branch.png)
 
 - 第二步: 上面确认设置后，依旧报错，改用下面方法
  ![](https://52lu.github.io/directionsImg/git/gitlab-use-http.png)
  
  
# Q6 git add后只增加了文件夹，但是没有文件。

使用git status查看，没有任何提交，后手动删除重新copy了文件，再次使用git status：
```
modified: vendor/phpgangsta/googleauthenticator/PHPGangsta/GoogleAuthenticator.php (modified content)
```

git add后只增加了文件夹，但是没有文件。手动Add: 
```
git vendor/phpgangsta/googleauthenticator/PHPGangsta/GoogleAuthenticator.php

报出错误信息： 
fatal: Pathspec 'xxx' is in submodule
```

解决方法:

-  发现vendor/phpgangsta/googleauthenticator/PHPGangsta/下 有.git文件 
-  所以使用下面命令：
 
```
git rm -rf --cached vendor/phpgangsta/googleauthenticator/PHPGangsta
git add vendor/phpgangsta/*
```

然后正常提交。