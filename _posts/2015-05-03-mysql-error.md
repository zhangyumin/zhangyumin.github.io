---
layout: post
title: ERROR 29 (HY000) File '/var/www/upload/*.txt' not found (Errcode 13)
category: [MySQL,战Error]
date: 2015-05-03
---

突然遇到mysql错误：ERROR 29 (HY000): File '/var/www/upload/*.txt' not found (Errcode: 13)

上传后的文件都在本地,没有理由找不到,于是百度之,发现可能是ubuntu的文件存取权限问题.

但是当我把目录和文件权限都chmod为777后还是出错，google了后发现：
<!-- more -->
Recent Ubuntu Server Editions (such as 10.04) ship with AppArmor and MySQL's profile might be in enforcing mode by default. You can check this by executing 

	sudo aa-status
like so:

	# sudo aa-status 5 profiles are loaded.5 profiles are in enforce mode./usr/lib/connman/scripts/dhclient-script /sbin/dhclient3 /usr/sbin/tcpdump /usr/lib/NetworkManager/nm-dhcp-client.action /usr/sbin/mysqld 0 profiles are in complain mode.1 processes have profiles defined.1 processes are in enforce mode :/usr/sbin/mysqld (1089)0 processes are in complain mode.


If mysqld is included in enforce mode, then it is the one probably denying the write. Entries would also be written in 
	/var/log/messages
when AppArmor blocks the writes/accesses. What you can do is edit
	/etc/apparmor.d/usr.sbin.mysqld
and add 
	/data/
and 
	/data/*
near the bottom like so:

	...
	/usr/sbin/mysqld {
	...
	/var/log/mysql/ r,
	/var/log/mysql/* rw,
	/var/run/mysqld/mysqld.pid w,
	/var/run/mysqld/mysqld.sock w,
	/data/ r,
	/data/* rw,
	}

And then make AppArmor reload the profiles.

	# sudo /etc/init.d/apparmor reload

大体内容就是，mysqld强制编码了，文件没有写入权限，于是我把该文件目录也加到mysqld(/etc/apparmor.d/usr.sbin.mysqld这个文件的底部，如上粗体部分):

	/var/www/upload/import/ r,

	/var/www/upload/import/* rw,

## 参考
> [http://www.phpddt.com/db/mysql-error-29.html](http://www.phpddt.com/db/mysql-error-29.html)
