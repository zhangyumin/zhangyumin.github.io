---
layout: post
title: 解决wineQQ中无法使用搜狗输入法(fcitx)的问题
category: [战Error]
date: 2016-07-10
---
昨天装好QQ后无法输入中文，用复制粘贴跟人聊天，在wineQQ下根本就打不开fcitx输入法，搜狗必然是不能用的。

但是在终端中打开的QQ却意外可以输入中文，一度令我很是费解，查到是环境变量有几个没设置好，开始动手解决。

<!-- more -->

找到wineQQ的安装目录，桌面图标点右键属性就能看到，或者用命令行搜索。

我的是/opt/longene/qq/qq.sh，直接使用vim打开，可以看到里面的运行参数。

将下面语句加在开始的位置，使启动qq的时候能够自动加载这些参数就可以了。
	
	export XMODIFIERS="@im=fcitx"
	export GTK_IM_MODULE="fcitx"
	export QT_IM_MODULE="fcitx"

OK保存关闭，重新打开QQ测试一下吧，应该是可以愉快的输入中文了。还有一种方法是跟之前解决sublime无法输入中文方式一样，在.desktop中直接加入这些参数，效果应该是一样的。