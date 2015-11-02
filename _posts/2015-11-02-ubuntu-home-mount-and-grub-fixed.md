---
layout: post
title: ubuntu系统grub修复及/home扩容
category: [Linux,战Error有感]
date: 2015-11-02
---
几天前重装了ubuntu系统，重装的时候有覆盖安装选项，以为能省去好多麻烦，不用再重新格式化分区了。然而七七八八环境都搭好了之后发现之前几百G的/home分区被保留下来了，当做一个普通的磁盘挂载在电脑上。于是打算将挂载的分区格式化后给新的/home扩容。用系统自带的“磁盘”格了后选择开机自动挂载到/home，重启。

显示挂载到home时出现错误，取消挂载，然后重启，压根就进不去系统了。
<!-- more -->

#Grub修复
进不去系统直接就显示到
	
	grub rescue>

想想系统选择菜单都没了，应该时找不到grub了，开始动手拯救grub。

1.在grub rescue中输入以下命令
	
	ls

可以看到所有磁盘分区的信息，列表大概是这个样子。
	
	(hd0,1)(hd0,2)(hd0,3)(hd0,4)(hd0,5)(hd0,6)(hd0,7)

2.按照上面的分区信息
	
	1)若有boot分区，则依次执行
	ls (hd0,x)/grub #x为分区号码，下同
	2)无boot分区，则依次执行
	ls (hd0,x)/boot/grub

3.直到找到一个分区，显示有grub.cfg等许多看起来像grub的文件，说明grub装在这里。

4.但是我是装的双系统，所以grub和真正的ubuntu并不在一起，可以用命令继续试，直到也找到ubuntu所在的分区，一定要记好分区的号码（grub分区和系统分区两个）。

5.找到了grub，就把grub的临时信息写下来进系统吧，假设我grub是在(hd0,5)分区上
	
	#根据上面是否有boot分区，执行以下命令
	set root=(hd0,5)
	set prefix=(hd0,5)/boot/grub
	insmod (hd0,5)/boot/grub/i386-pc/normal.mod
	或者
	set root=(hd0,5)
	set prefix=(hd0,5)/grub
	insmod (hd0,5)/grub/i386-pc/normal.mod
我装了双系统，所以normal.mod在grub/i386-pc中，否则也可能在grub中，用2中的ls命令仔细找找这个文件

6.接下来执行
	
	normal
发现久违的grub菜单出现了，直接选ubuntu系统后又出错了，错误信息如下：（没有出错就直接看第7步吧）
	Give up waiting for root device. Common problems: 
	-boot args (cat.proc/cmdline) 
	-check root delay=(did the system wait long enough?) 
	-check root=(did teh system wait for the right device?) 
	-miss modules (cat/proc/modules;ls/dev) 
	ALERT! /dev/disk/by-uuid/acc3414d-926c-453c-b458-cf47088d77d2 does not exist.dropping to a shell!
这样子看起来是找不到系统的文件，或者在这个uuid是 acc3414d-926c-453c-b458-cf47088d77d2 的磁盘上找不到系统

命令行里输入
	
	ls -l /dev/disk/by-uuid/ 记录下来系统所在分区的uuid。
重启，按照上面的步骤再来一遍，又见到了久违的grub，这次可不能在直接选系统了，高亮ubuntu系统，按下e键编辑grub启动选项。

其中有一行是Linux ... /dev/disk/by-uuid/acc3414d-926c-453c-b458-cf47088d77d2 ... ，没错就是它了，把by-uuid/后边的一串uuid改成你系统所在分区的uuid（上面记下来的）。

然后按ctrl+x根据此引导启动系统，就能见到你的桌面了。

7.还没完，记得把grub信息更新保存，总不能每次启动都重写上面部分。
	
	sudo update-grub
	sudo grub-install /dev/sda（系统在sda盘的第七个分区，所以此处是sda，根据电脑情况修改）
至此，grub修复部分结束。

#/home扩容
grub修复了，系统能用了，800G的盘还得继续扩到/home下。

1.格式化分一下区，记下它的分区号码（比如/dev/sda8)

2.切到root用户，根目录下新建一个copy的文件夹，然后把分区挂载上。
	
	mkdir /copy
	mount /dev/sda8 /copy/

3.将/home下的数据全部复制到copy目录下，ls命令查看一下copy目录下是否是原/home的内容
	
	mv /home/ /copy/
	ls /copy/

4.确认copy目录下没有少内容后，将sda8分区卸载掉
	
	umount /dev/sda8

5.删除/home目录并且重新创建/home目录
	
	rm -rf /home
	mkdir /home

6.新分区（sda8）挂载到新的/home下
	
	mount /dev/sda8 /home/

7.将/home/home下的内容移动到/home中去
	
	mv /home/home/(用户名) /home

8.最后别忘了在/etc/fstab中添加内容病设置开机自动挂载
	
	vim /etc/fstab
	#增加
	/dev/sda8 /home ext4    defaults        0       0

9.重启并查看/home分区下容量，确认是800G，扩容搞定。
