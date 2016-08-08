---
layout: post
title: ubuntu16.04安装rCurl及rJava包失败的解决方案
category: [战Error]
date: 2016-07-03
---
安装R包总是会出现很多问题，不是因为版本太旧，就是系统缺少依赖，安装过程输出信息有点略多，经常找不到有用的报错信息。

<!-- more -->

## rCurl错误及解决

1.出错信息如下：
	
	* installing *source* package ‘RCurl’ …
	** 成功将‘RCurl’程序包解包并MD5和检查
	checking for curl-config… no
	Cannot find curl-config
	ERROR: configuration failed for package ‘RCurl’
	* removing ‘/home/ypchen/R/x86_64-pc-linux-gnu-library/2.14/RCurl’

	下载的程序包在
	‘/tmp/Rtmpz1sA0G/downloaded_packages’里
	警告信息：
	In install.packages(“RCurl”) : 安装程序包‘RCurl’时退出状态的值不是0

2.缺少curl-config
	
	sudo apt-get install libcurl-dev
	sudo apt-get install libcurl4-gnutls-dev

上面两句总有一个能解决问题，都试一下就好

## rJava错误及解决

错误信息如下：
	
	checking Java support in R... present:
	interpreter : '/usr/bin/java'
	archiver    : '/usr/bin/jar'
	compiler    : '/usr/bin/javac'
	header prep.: '/usr/bin/javah'
	cpp flags   : '-I/usr/lib/jvm/java-6-sun-1.6.0.20/jre/../include -I/usr/lib/jvm/java-6-sun-1.6.0.20/jre/../include/linux'
	java libs   : '-L/usr/lib/jvm/java-6-sun-1.6.0.20/jre/lib/amd64/server -L/usr/lib/jvm/java-6-sun-1.6.0.20/jre/lib/amd64 -L/usr/lib/jvm/java-6-sun-1.6.0.20/jre/../lib/amd64 -L -L/usr/java/packages/lib/amd64 -L/usr/lib64 -L/lib64 -L/lib -L/usr/lib -ljvm'
	checking whether JNI programs can be compiled... yes
	checking JNI data types... configure: error: One or more JNI types differ from the corresponding native type. You may need to use non-standard compiler flags or a different compiler in order to fix this.
	ERROR: configuration failed for package ‘rJava’

经常出现这种我装了Java环境但是rJava识别不出来的错误，所以需要安装一下
	
	sudo apt-get install r-cran-rjava

重新执行安装rJava即可