---
layout: post
title: /etc/update-motd.d/90-update-motd.d/90-updates-available exited with return code 255
category: [战Error]
date: 2015-12-06
---
昨晚收到消息实验室的服务器跪了，原因是进行了ubuntu的软件升级。

立马就回忆起了当年升级ubuntu系统时的惨痛经历，应该是环境变量出了问题。

得知能够进系统但登陆不进去才稍微感觉良好一点，毕竟还可以拯救，避免了接二连三的重装便是极好的。
<!-- more -->

有了上次升级系统失败的经历，上来先登陆遭遇了诡异的蓝色屏幕，然后就一直停在了这个页面，鼠标还是能转的。

使用Ctrl + alt + f1进入控制台模式，输入用户名和密码登陆控制台看输出的错误信息，得到了下面的信息：

	E:Error:BorkenCount > 0run-parts:/etc/update-motd.d/90-update-motd.d/90-updates-available exited with return code 255.

很明显的是这次的错误跟之前系统升级失败不同，不是环境变量的事儿。只更新了软件没有升级系统自然不太可能影响到环境变量。

必应一下，这个90-updates-available是跟登陆及欢迎信息控制有关。

估计是某个软件更新失败导致整个更新崩溃掉了，或者被人为的中断更新了。

首先就恢复更新完成整个安装过程，在控制台输入
	
	sudo dpkg -- configure -a

来进行安装更新，很顺利的完成。如果过程中有软件报错，可以尝试用下面的语句修复一下依赖关系：
	
	sudo apt-get install -f

然后重启，就能看到久违的ubuntu桌面了。

