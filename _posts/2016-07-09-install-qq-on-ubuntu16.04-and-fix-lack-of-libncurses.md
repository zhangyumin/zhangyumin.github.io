---
layout: post
title: ubuntu16.04安装qq并解决缺少libncurses5-dev的问题
category: [战Error]
date: 2016-07-09
---
用ubuntu系统也有好几年了，QQ一直都没找到很好的解决方法，网页版的QQ能收到消息，但是传文件又不行，用网盘就更加繁琐了，装上wineQQ后，终于整个世界都清净了。

用的是 longene-wine-qq，15年底最后更新，大部分功能都能正常使用，但是还是有以下的问题，不过已经很不错了：

+ 无法保存密码、自动登录
+ 点击密码输入框有时不能激活，需要多点击几次
+ 程序内选择离线后无法再次上线，需要关闭程序，重新启动 
+ 其他很多很多没有测试到的问题

<!-- more -->

# 安装longene-wine-qq

Wine QQ 7.8

下载地址：[WineQQ7.8-20151109-Longene](http://www.longene.org/download/WineQQ7.8-20151109-Longene.deb)

百度网盘：[WineQQ7.8-20151109-Longene](http://pan.baidu.com/s/1kTu9ZUZ)

下载下来以后直接使用dpkg -i WineQQ7.8-20151109-Longene.deb安装即可。

安装完成后第一次运行建议在终端中直接执行qq，看输出是否报错。

#解决缺少libncurses5-dev的问题

果不其然还是报错了，错误信息是这样的：
	
	Wine cannot find the ncurses library (libncurses.so.5)
	Application tried to create a window, but no driver could be loaded.

很明显是缺少依赖了，我搜了一下系统中是有libncurses.so.5的，再回去看看说明，原来这个wineQQ支持的是32位系统

而新装的64位ubuntu16.04很明显缺少32位的依赖，执行下面安装就可以了
	
	sudo apt-get install  lib32ncurses5-dev 