---
title: phpstorm快捷键
date: 2016-06-08 10:19:06
categories: others
tags:
---
1. 快捷键
F5   复制文件/文件夹
F6   移动
CTRL+N   查找类
CIRL+B   找变量的来源，跳到变量申明处
CTRL+R   当前窗口替换文本
CTRL+E   最近打开的文件
CTRL+ []   光标移动到 {}[]开头或结尾位置
ctrl+shift+enter(智能完善代码
SHIFT+ESC  光 标返回编辑框,关闭无用的窗口
Alt+1 打开侧边栏
F3   向下查找关键字出现位置
SHIFT+F3  查找上一个
F4   查找变量来源
ALT+SHIFT+C  查找修改的文件，最近变更历史
CTRL+ALT+I  自动缩进
CTRL+ALT+O  优化导入的类和包
CTRL+P   方法参数提示，显示默认参数
CTRL+J   自动代码提示，自动补全
CTRL+ALT+T  把选中的代码放在 TRY{} IF{} ELSE{} 里
ALT+INSERT  生成代码(如GET,SET方法,构造函数等)
CTRL+H        显 示类结构图
CTRL+Q   显示代码注释
CTRL+ []   光标移动到 {}[]开头或结尾位置
CTRL+SHIFT+[]    选中块代码，可以快速复制
CTRL+ALT+F12  资源管理器打开文件夹，跳转至当前文件在磁盘上的位置
SHIFT+ALT+INSERT 竖编辑模式
alt + '7': 显示当前的类/函数结构。类似于eclipse中的outline的效果。试验了一下，要比aptana的给力一些，但还是不能完全显示prototype下面的方法名。

2. 隐藏其他东西
隐藏底下条 view/Status Bar
隐藏四周条 view/Tool Buttons
隐藏包路径 view/Navigation Bar
隐藏面包屑 control+alt+s 搜索bread 出来 show breadcrumbs 去掉
隐藏文件名 Windows/Tabs Placement/None
自动换行   File->Settings->Editor->general 勾选 “ Use soft wrap in editor ”
注释在顶行 Preferences->Editor->Code Style->PHP->Other里面取消Line comment at first column之前的勾选就行了
大括号换行 setting -> editor -> code style -> php -> Wrapping and Braces -> Braces placement -> In function declaration Next line
等号对齐  setting -> editor -> code style -> php -> Wrapping and Braces -> Assignment statement ->Align consecutive assignments	true
数组对齐  setting -> editor -> code style -> php -> Other -> Align key-value pairs
显示行数  setting -> editor ->Appearance -> Show line numbers
修改SSH字体大小 setting -> editor -> Colors & Fonts -> Console Font -- Size

3. 破解phpstorm
选择Lincense server
如果http://15.idea.lanyus.com/方法无法激活。可以将其替换为http://jetbrains.tencent.click
或者http://idea.lanyus.com/
破解2017.2 http://imsxm.com/ 或者 http://www.imsxm.com/
2017.3 http://idea.ibdyr.com  或者 https://www.imsxm.com/
方法一. 通过Licence Server 激活PHPStorm（快速）
方法原理是通过搭建服务器激活,不过网上有许多搭建好的。
http://idea.goxz.gq
http://v2mc.net:1017
http://idea.imsxm.com （2016版）
http://idea.ibdyr.com
注：不一定长期有效
方法二. 直接用浏览器打开 http://idea.lanyus.com/ （推荐）
点击页面中的“获得注册码”，然后在注册时切换至Activation Code选项，输入获得的注册码一长串字符串，便可以注册成功了！（推荐用这种方式）
搭建phpstorm服务器
https://github.com/a252937166/new-idea-server
服务器:Cannot obtain ticket http://idea.sujianxun.com:1027

4. 支持中文搜索
setting -> Appearance & Behavior -> Appearance 去掉 Override default fonts by (not recommended): 打勾选项

5. 2019.2去掉ctrl+e重复点击不可顺序往下选中文件
setting -> keymap -> Main menu -> View -> Toggle Changed Only Files 去掉快捷键

6. PHPStorm使用PHP7新特性出现红色波浪错误
setting -> Languages & Frameworks -> PHP 发现PHP language level不可修改 -> Composer 去掉Synchronize IDE Settings with composer.json选项 点击Apply -> 返回 -> PHP 修改PHP language level 选项 选择php7.2
