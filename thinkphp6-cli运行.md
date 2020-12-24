---
title: thinkphp6-cli运行
date: 2020-12-24 10:12:30
categories: thinkphp
tags:
---
```bash
thinkphp6默认不支持命令行CLI模式访问控制器
打开 /vendor/topthink/framework/src/think/Request.php文件
搜索pathinfo函数 (601行)
找到 } elseif (false !== strpos(PHP_SAPI, 'cli')) { (611行)
将下一行(612行的)
$pathinfo = strpos($this->server('REQUEST_URI'), '?') ? strstr($this->server('REQUEST_URI'), '?', true) : $this->server('REQUEST_URI');
注释掉(保证核心文件完整性) 并在上一行加上
$pathinfo = isset($_SERVER['argv'][1]) ? $_SERVER['argv'][1] : '';
```
