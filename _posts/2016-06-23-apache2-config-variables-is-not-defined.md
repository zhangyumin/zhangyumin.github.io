---
layout: post
title: apache2提示Syntax error on line 74 of /etc/apache2/apache2.conf
category: [经验积累]
date: 2016-06-23
---
ubuntu16.04升级后，问题真的是一大堆。首先是mysql升级失败无法使用，然后是apache2启动报错，从提示信息来看是找不到配置变量。

<!-- more -->

执行apache2 -V查看apache2的版本信息时，出现如下提示：
{% highlight shell %}	
[Fri Nov 29 17:35:43.942472 2013] [core:warn] [pid 14655] AH00111: Config variable ${APACHE_LOCK_DIR} is not defined
[Fri Nov 29 17:35:43.942560 2013] [core:warn] [pid 14655] AH00111: Config variable ${APACHE_PID_FILE} is not defined
[Fri Nov 29 17:35:43.942602 2013] [core:warn] [pid 14655] AH00111: Config variable ${APACHE_RUN_USER} is not defined
[Fri Nov 29 17:35:43.942613 2013] [core:warn] [pid 14655] AH00111: Config variable ${APACHE_RUN_GROUP} is not defined
[Fri Nov 29 17:35:43.942627 2013] [core:warn] [pid 14655] AH00111: Config variable ${APACHE_LOG_DIR} is not defined
[Fri Nov 29 17:35:43.947913 2013] [core:warn] [pid 14655] AH00111: Config variable ${APACHE_LOG_DIR} is not defined
[Fri Nov 29 17:35:43.948051 2013] [core:warn] [pid 14655] AH00111: Config variable ${APACHE_LOG_DIR} is not defined
[Fri Nov 29 17:35:43.948075 2013] [core:warn] [pid 14655] AH00111: Config variable ${APACHE_LOG_DIR} is not defined

AH00526: Syntax error on line 74 of /etc/apache2/apache2.conf:
Invalid Mutex directory in argument file:${APACHE_LOCK_DIR}
{% endhighlight %}

apache2从/etc/apache2/envvars文件中读取配置变量，看情况应该是读取失败了，手动执行一下
{% highlight shell %}
source /etc/apache2/envvars
{% endhighlight %}

然后重新查看一下apache2 -V，就能看到正确的信息了。
{% highlight shell %}	
Server version: Apache/2.4.18 (Ubuntu)
Server built:   2016-04-15T18:00:57
Servers Module Magic Number: 20120211:52
Server loaded:  APR 1.5.2, APR-UTIL 1.5.4
Compiled using: APR 1.5.2, APR-UTIL 1.5.4
Architecture:   64-bit
Server MPM:     prefork
  threaded:     no
    forked:     yes (variable process count)
Server compiled with....
 -D APR_HAS_SENDFILE
 -D APR_HAS_MMAP
 -D APR_HAVE_IPV6 (IPv4-mapped addresses enabled)
 -D APR_USE_SYSVSEM_SERIALIZE
 -D APR_USE_PTHREAD_SERIALIZE
 -D SINGLE_LISTEN_UNSERIALIZED_ACCEPT
 -D APR_HAS_OTHER_CHILD
 -D AP_HAVE_RELIABLE_PIPED_LOGS
 -D DYNAMIC_MODULE_LIMIT=256
 -D HTTPD_ROOT="/etc/apache2"
 -D SUEXEC_BIN="/usr/lib/apache2/suexec"
 -D DEFAULT_PIDLOG="/var/run/apache2.pid"
 -D DEFAULT_SCOREBOARD="logs/apache_runtime_status"
 -D DEFAULT_ERRORLOG="logs/error_log"
 -D AP_TYPES_CONFIG_FILE="mime.types"
 -D SERVER_CONFIG_FILE="apache2.conf"
{% endhighlight %}


