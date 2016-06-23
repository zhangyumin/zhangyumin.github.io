---
layout: post
title: ubuntu更新16.04后phpmyadmin报500错误
category: [经验积累]
date: 2016-06-23
---
更新后无法使用phpmyadmin，浏览器直接报500错误，页面无法显示（innternal server error），重装phpmyadmin也无济于事，本以为是apache2的问题，经过一系列排除后，应该是phpmyadmin自己的问题。

<!-- more -->

先查到phpmyadmin的安装指导，写到要修改config.example.inc.php为config.inc.php然后修改当中的配置项。

之前14.04系统安装的时候自动给配置好了，而现在需要自己动手操作，如果不需要修改配置也可以只改好文件名使用默认配置。

再次刷新还是报错，到/var/log/apache2/error.log中查apache2的错误信息，有这么一条：
	
	PHP Fatal error: require_once(): Failed opening required './libraries/php-gettext/gettext.inc' (include_path='.') in /usr/share/phpmyadmin/libraries/select_lang.lib.php on line 370

结果就很明显了，应该是升级了php7.0以后缺少了 php-gettext，执行下面语句就可以解决了
	
	sudo apt-get install php-gettext


