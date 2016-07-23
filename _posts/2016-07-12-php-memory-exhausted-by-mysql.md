---
layout: post
title: Fatal error:Allowed memory size of unknown bytes exhausted (tried to allocate 32 bytes) in unknown.php on line unknown
category: [战Error]
date: 2016-07-12
---
上一次碰到这个问题还是刚开始学PHP的时候，果断的在php.ini中把memory_limit改大，重启apache再试，仍然报错。

立马换第二方案，程序中声明ini_set('memory_limit', '2560M')，重启apache还是挂。

再后来我仔细的盯着三行报错信息看了好几遍，终于发现是mysql这里出问题了。
<!-- more -->

刚装的系统理论上基本都是参数有问题，可是为什么mysql会占用这么大的内存。

查到的原因如下：
	
	PHP MySQL查询(mysqli,pdo_mysql)默认使用缓冲模式.
	也就是说查询结果将一次性从MySQL传输到PHP进程内存中,
	这时可以统计结果集的行数,以及移动结果集指针.
	缓冲模式下,如果结果集很大,那么PHP进程也会占用大量的内存,
	直到结果集被unset或者free.
	store_result也用于缓冲模式,所有结果一次性存储到PHP进程中:
	mysqli::store_result
	mysqli_stmt::store_result
	如果PHP的MySQL数据库驱动底层用的是libmysqlclient,那么memory_limit不能统计到结果集占用的内存,
	除非结果集已经赋值给PHP变量,如果底层使用mysqlnd作为驱动时则可以统计到(PHP从5.4开始默认底层默认使用mysqlnd).
	无缓冲模式下执行的查询将会返回一个resource资源引用,位于MySQL查询结果等待PHP获取.
	无缓冲模式下,PHP进程占用的内存很少,但会增大MySQL服务器的负载.
	在PHP取回所有结果前,在当前数据库连接下不能发送其他的查询请求.
	无缓冲查询简称use_result.
	总结:
	当结果集不大时,或者需要在读取所有行前获取结果集行数时,使用缓冲查询(默认).
	当结果集很大时,使用无缓冲查询,避免PHP进程占用大量的内存.

解决方法很简单，将 PDO::MYSQL_ATTR_USE_BUFFERED_QUERY 置为false就可以了，比如：
{% highlight php %}
<?php
	$pdo = new PDO('mysql:host=127.0.0.1', 'foo', 'bar', array(
	    PDO::ATTR_ERRMODE=>PDO::ERRMODE_EXCEPTION,
	));
	$pdo->setAttribute(PDO::MYSQL_ATTR_USE_BUFFERED_QUERY, false);
?>
{% endhighlight %}

当然对于Mysql，你也可以使用mysql_unbuffered_query()来代替mysql_query()
