---
layout: post
title: Fatal error: Allowed memory size of unknown bytes exhausted (tried to allocate 32 bytes) in unknown.php on line unknown
category: [转载]
date: 2016-07-11
---
上一次碰到这个问题还是刚开始学PHP的时候，果断的在php.ini中把memory_limit改大，重启apache再试，仍然报错。

立马换第二方案，程序中声明ini_set('memory_limit', '2560M')，重启apache还是挂。

再后来我仔细的盯着三行报错信息看了好几遍，终于发现是mysql这里出问题了。
<!-- more -->


