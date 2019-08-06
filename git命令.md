---
title: GIT命令
date: 2017-05-10 10:20:13
categories: git
---
```bash
强制覆盖本地文件
git fetch --all
git reset --hard origin/master
储藏缓存文件
git stash
查看列表
git stash list
清空你所有的内容
git stash clear
删除第一个队列
git stash drop stash@{0}
服务器上出现Your branch is ahead of 'origin/master' by 2 commits.
解决办法: 强制覆盖本地文件
git fetch --all
git reset --hard origin/master
```
