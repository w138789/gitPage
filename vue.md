---
title: vue
date: 2021-02-19 10:20:13
categories: vue
---
```bash
Vue路由history模式踩坑记录：nginx配置解决请求404问题
location / {
　　root   D:\Test\exprice\dist;
　　index  index.html index.htm;
　　try_files $uri $uri/ /index.html; #增加这一行
```