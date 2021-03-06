---
layout: post
title: git使用SSH进行认证链接并解决github的push每次都需要输入用户名和密码
category: [经验积累]
date: 2016-06-16
---
每次push推送都需要输入用户名密码进行push非常麻烦，配置了SSH的话就能够免除这些繁琐的步骤了。

而在github上创建了ssh key，并成功启用后，每次push仍然需要输入用户名密码。

那原因是之前默认用了https的方式进行push了，下面也会把它替换成ssh方式推送。

<!-- more -->

# 配置SSH

### 1.创建SSH key
	
{% highlight shell %}
//先到用户主目录下查看是否已经生成过ssh key
cd ~/.ssh
//如果没有使用下面语句创建SSH key
ssh-keygen -t rsa -C "youremail@host.com"
//一路回车，如果有需要可以设置密码
{% endhighlight %}
完成后我们可以在用户主目录里的.ssh文件夹下找到id_rsa(私钥）和id_rsa.pub（公钥）两个文件。

### 2.添加到Github

登陆到Github，在settings的SSH and GPG keys中点击new SSH key，填写任意Title后，就爱那个id_rsa.pub中所有内容添加到文本框中，点击Add Key就可以看到自己添加的Key了。
{% highlight shell %}
//测试
ssh git@github.com
{% endhighlight %}
如果显示
{% highlight shell %}
Hi username! You have successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
{% endhighlight %}
就表示成功了。

# 替换push方式
进入指定的git文件夹，在terminal中输入git remote -v

就能够看到各个路径的push、fetch方式，比如：

origin https://github.com/username/demo.git (fetch)

origin https://github.com/username/demo.git (push)

我们可以用下列语句把它换成ssh方式的：

	
	1. git remote rm origin

	2. git remote add origin git@github.com:username/demo.git

	3. git push origin master
