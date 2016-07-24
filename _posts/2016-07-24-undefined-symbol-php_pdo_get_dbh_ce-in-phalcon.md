---
layout: post
title: Phalcon "undefined symbol: php_pdo_get_dbh_ce in Unknown on line 0"
category: [战Error]
date: 2016-07-24
---
按照官网的步骤安装phalcon，一切都顺利，php_info()也能打印出phalcon.so相关的信息。

可就是一直在不停的报错：undefined symbol: php_pdo_get_dbh_ce in Unknown on line 0”
<!-- more -->

很多人都碰见过这样的问题，原因是pdo在phalcon之后加载了，这样phalcon就没法调用php_pdo_get_dbh_ce了。

解决方法是将/etc/php/5.6/cli/conf.d/30-phalcon.ini中的那个数字权重改的比pdo大，这样就能保证pdo在phalcon之前加载了。

大部分人改完就OK了，但是实际上extension=phalcon.so是不应该被写到php.ini中的，而只是在/etc/php/5.6/cli/conf.d/30-phalcon.ini中。

所以将php.ini中的extension=phalcon.so注释掉，然后重启apache就可以了。
