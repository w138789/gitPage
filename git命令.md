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
Git如何撤销merge操作
第一步
查看git 的版本信息，找到merge前最近提交的版本号，并且复制对应的版本号
git log
第二步
git reset 版本号
现在暂存区，与版本库已经撤销回之前的版本了
第三步
查看工作区与暂存区的区别，当前工作区还是merge后的代码
git status
第四步
撤回暂存区到工作区
git checkout -- fileName
ok了，现在工作区与暂存区，版本库全是merge之前的代码了
```
