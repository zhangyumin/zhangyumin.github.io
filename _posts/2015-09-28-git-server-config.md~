---
layout: post
title: 搭建Git服务器及web站点自动化部署
category: [Git,经验积累]
date: 2015-09-28
---
作为预备役码农,从半年前开始学习使用Github,之后一直将代码托管在线上仓库中.后来网站代码需要多人协同工作,而且项目不太适合放在Github中,便需要将实验室服务器搭建成私有的Git服务器.

Git的个人服务器搭建并不麻烦,但是实验室只有这一台服务器,需要在提交推送代码的同时,将网站同时也部署好,以达到边修改边查看页面效果的目的.
<!-- more -->

#Git个人服务器的搭建及测试

##Git的安装
1.在服务器端和开发机端都需要在shell中输入以下命令来安装Git
	
	#ubuntu系统Git的安装	
	sudo apt-get install git-core

2.因为netbeans上对Git的支持很好,所有没有配置常用的ssh协议,这个协议的最直接好处就在于每次向Git提交推送都不需要输密码,由于没有尝试就不写步骤了,Github官方教程可以一看[http://help.github.com/articles/generating-ssh-keys](http://help.github.com/articles/generating-ssh-keys)

##Git服务器端配置
1.新建用户,作为Git服务器代码仓库的管理者.
	
	sudo useradd -m github
	#-m功能是Create the user's home directory if it does not exist(若用户home目录不存在,则创建)
	sudo passwd github

2.建立Git仓库目录
	
	#我的Git仓库目录在/home/github/website/中,因人而异
	sudo mkdir /home/github/website
	#↓将仓库目录权限设置为仅github用户可操作
	sudo chown github:github /home/github/website
	sudo chmod 700 /home/github/website

3.创建仓库文件夹
	
	#切换到github用户
	su github
	#进入website目录
	cd /home/github/website/
	#创建仓库文件夹
	mkdir repository
	#初始化为bare仓库
	git init --bare

git init和git init --bare的区别:

用git init初始化的版本库,其保存的都是原文件,但某些用户的push操作可能导致冲突.而git init --bare创建的是裸仓库,它不保存原文件而只保存git历史提交的版本信息,并不允许用户直接在上面进行git操作.

##Git客户机(开发)操作
Git服务器配置好后就可以直接git remote并提交推送到远程仓库中了.

1.创建本地Git仓库目录
	
	#创建目录并git初始化
	cd /var/www/website/
	git init

2.添加文件及提交测试
	
	#创建index.php并将hello world写入
	touch index.php | echo 'hello world' > index.php
	#添加跟踪
	git add index.php
	#添加远程主机,username@yourserverip:address
	git remote add origin github@192.168.1.100:/home/github/website/repository
	#提交
	git commit -am 'new page'
	#推入
	git push origin master

3.如果推入成功,那么在远程仓库已经能够得到最新的版本了.

#Git自动部署
Git本身可以调用自定义的Hooks脚本,分为两类:客户端和服务器端.客户端Hooks用于客户端的操作,例如提交和合并.服务器端Hooks用于服务器端操作,比如接收推入的提交.在此处,我们自动部署的思路就是每当服务器接收到客户机(开发)的推入,就自动将更新pull到apache的DocumentRoot中去.

Git的hooks主要包括:
	
	applypatch-msg
	post-update
	pre-rebase
	commit-msg
	pre-applypatch
	update
	post-commit
	pre-commit
	post-receive
	prepare-commit-msg

此处我们用post-receive这个hook,即在post(push)请求之后执行.

1.在服务器端Git仓库目录的hooks中编辑post-receive
	
	#切换用户获得权限
	su github
	cd /home/github/website/repository/hooks
	#创建或编写post-receive文件
	vim post-receive

2.在apache的DocumentRoot中clone仓库
	
	cd /var/www
	mkdir website
	cd website
	git clone /home/github/website/repository ./
	
写了很简单的部署代码,因为push后还需要其他验证,所以仍需要更新,此处仅做最简单的pull操作作为参考
	
	#!/bin/sh
	unset $(git rev-parse --local-env-vars)
	wwwPath=/var/www/website
	cd $wwwPath
	git pull origin master

最后需要将脚本改为允许执行
	
	chmod +x post-receive

刚编写的时候想当然认为推入后直接pull到目录即可,就没有写
	
	unset $(git rev-parse --local-env-vars)

这一句.远程服务器一直不能pull到最新数据,以为是权限问题,但通过调试发现post-receive有执行,但终端有报错
	
	remote :fatal: Not a git repository:'.';

网上搜了一下发现Git的hooks在要执行的时候会调用GIT_DIR这个环境变量,总之写入unset之后问题就解决了,也能pull到最新数据了.

注:git的自动部署需要权限,务必确认推入的用户拥有写入apache的DocumentRoot的权限.
