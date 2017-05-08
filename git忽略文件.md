---
title: GIT忽略文件
date: 2017-04-10 10:20:13
categories: git
---
开发过程中由开发工具生成的文件一般不需要提交，但每次开发工具会自动去修改这些文件，
每次都要去提交这些东西，不提交会有一系列问题，很烦人。 
可以通过配置.gitignore文件让Git不在跟踪记录这些文件。
心血来潮去配置的时候，发现配置过的文件并没有生效，伤感万分，别担心有解决方案。
先找原因，因为.gitignore只能忽略那些原来没有被track的文件，
如果某些文件已经纳入版本管理中，则修改.gitignore不会生效。
解决办法就是先把本地缓存删除（改成未track状态），然后再提交”：
```bash
git rm -r --cached .
git add .
git commit -m "update .gitignore"
```