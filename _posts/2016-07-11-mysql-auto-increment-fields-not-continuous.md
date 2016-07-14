---
layout: post
title: MySQL中自增主键不连续的问题
category: [转载]
date: 2016-07-10
---
自增长是一个很常见的数据属性，在MySQL中大家都很愿意让自增长属性的字段当一个主键。特别是InnoDB，因为InnoDB的聚集索引的特性，使用自增长属性的字段当主键性能更好，这里要说明下自增主键需要注意的几个事项。

<!-- more -->

*问题一：表锁*

在MySQL5.1.22之前，InnoDB自增值是通过其本身的自增长计数器来获取值，该实现方式是通过表锁机制来完成的（AUTO-INC LOCKING）。锁不是在每次事务完成后释放，而是在完成对自增长值插入的SQL语句后释放，要等待其释放才能进行后续操作。比如说当表里有一个auto_increment字段的时候，innoDB会在内存里保存一个计数器用来记录auto_increment的值，当插入一个新行数据时，就会用一个表锁来锁住这个计数器，直到插入结束。如果大量的并发插入，表锁会引起SQL堵塞。

在5.1.22之后，InnoDB为了解决自增主键锁表的问题，引入了参数innodb_autoinc_lock_mode，该实现方式是通过轻量级互斥量的增长机制完成的。它是专门用来在使用auto_increment的情况下调整锁策略的，目前有三种选择：

插入类型说明：
	
	INSERT-LIKE：指所有的插入语句，比如 INSERT、REPLACE、INSERT…SELECT、REPLACE…SELECT,LOAD DATA等
	Simple inserts：指在插入前就能确定插入行数的语句，包括INSERT、REPLACE，不包含INSERT…ON DUPLICATE KEY UPDATE这类语句。
	Bulk inserts：指在插入前不能确定得到插入行的语句。如INSERT…SELECT,REPLACE…SELECT,LOAD DATA.
	Mixed-mode inserts:指其中一部分是自增长的，有一部分是确定的。

0：通过表锁的方式进行，也就是所有类型的insert都用AUTO-inc locking。

1：默认值，对于simple insert 自增长值的产生使用互斥量对内存中的计数器进行累加操作，对于bulk insert 则还是使用表锁的方式进行。

2：对所有的insert-like 自增长值的产生使用互斥量机制完成，性能最高，并发插入可能导致自增值不连续，可能会导致Statement 的 Replication 出现不一致，使用该模式，需要用 Row Replication的模式。

在mysql5.1.22之前，mysql的INSERT-LIKE语句会在执行整个语句的过程中使用一个AUTO-INC锁将表锁住，直到整个语句结束（而不是事务结束）。因此在使用INSERT…SELECT、INSERT…values(…),values(…)时，LOAD DATA等耗费时间较长的操作时，会将整个表锁住，而阻塞其他的insert-like,update等语句。推荐使用程序将这些语句分成多条语句，一一插入，减少单一时间的锁表时间。

*解决：*

通过参数innodb_autoinc_lock_mode =1/2解决，并用simple inserts 模式插入。


*问题二：自增主键不连续*

5.1.22后 默认：innodb_autoinc_lock_mode = 1 
直接通过分析语句，获得要插入的数量，然后一次性分配足够的auto_increment id，只会将整个分配的过程锁住。
	
{% highlight mysql %}
root@localhost : test 04:23:28>show variables like 'innodb_autoinc_lock_mode';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_autoinc_lock_mode | 1     |
+--------------------------+-------+
1 row in set (0.00 sec)

root@localhost : test 04:23:31>create table tmp_auto_inc(id int auto_increment primary key,talkid int)engine = innodb default charset gbk;
Query OK, 0 rows affected (0.16 sec)

root@localhost : test 04:23:35>insert into tmp_auto_inc(talkid) select talkId from talk_dialog limit 10;
Query OK, 10 rows affected (0.00 sec)
Records: 10  Duplicates: 0  Warnings: 0

root@localhost : test 04:23:39>show create table tmp_auto_inc\G;
*************************** 1. row ***************************
       Table: tmp_auto_inc
Create Table: CREATE TABLE `tmp_auto_inc` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `talkid` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=16 DEFAULT CHARSET=gbk
1 row in set (0.00 sec)
{% endhighlight %}