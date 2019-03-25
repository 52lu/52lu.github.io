---
title: Git相关的问题
date: 2016-11-23
tags:
 - git
categories:
 - git
---

### Q1 解决git pull/push每次都需要输入密码问题

解决办法:
- 进入你的项目目录，输入：
- > git config --global credential.helper store

### Q2 git无法pull仓库refusing to merge unrelated histories
> 解决方法: git pull --allow-unrelated-histories

### Q3  error: cannot lock ref 'refs/remotes/origin/..': ref refs/remotes/origin/.. is at 1907...927a1c but expected 8a5b...04b8

解决方法:
``` 
# rm .git/refs/remotes/origin/dev
# git fetch

```