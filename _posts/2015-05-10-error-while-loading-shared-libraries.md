---
layout: post
title: error while loading shared libraries libpcre.so.1 的解决方法 
category: 战error有感
date: 2015-05-10
---

在ubuntu下安装fastq-tool程序时,提示需要首先安装perl的PCRE库,在官网下载过后一路安装完毕,继续执行安装fastq-tool时缺提示:error while loading shared 
libraries: libpcre.so.1 这种错误,在网上搜索一番后,终于找到了解决方法.
 
	./fastq-tool: error while loading shared libraries: libpcre.so.1:cannot open shared object file: No such file or directory

出现这类错误表示,系统不知道libpcre.so.1放在哪个目录下，这时候就要在/etc/ld.so.conf中加入libpcre.so.1所在的目录。

一般而言,安装好的so.1文件会存放在/usr/local/lib这个目录底下,去这个目录底下找，果然发现自己所需要的.so.1文件。

所以,用root权限在/etc/ld.so.conf中加入/usr/local/lib这一行。（如果是加在第二行的话应该在第一行末尾空一格然后加上“\”换行符）

![error](/images/20150510.jpg)

保存之后，再运行:/sbin/ldconfig –v更新一下配置即可。

## 参考
> [http://blog.csdn.net/dumeifang/article/details/2963223](http://blog.csdn.net/dumeifang/article/details/2963223)
