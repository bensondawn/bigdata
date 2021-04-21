# Ntp服务端ntp.conf配置

![](E:\GitStorage\bigdata\picture\20171013130739105562427169.png)

> **1：第一个红框：确保localhost（这个常用的IP地址用来指Linux服务器本身）有足够权限.使用没有任何限制关键词的语法，红框的ip地址是本机的地址，一般可以不设置，默认使用下面两行即可！**
>
> **2：第二个红框：集群所在网段的网关（Gateway），子网掩码（Genmask），可以不配置，默认注释掉即可，用于限制访问的ip地址范围。**
>
> **3：**
>
> ```
> # 默认的一个内部时钟数据，用在没有外部 NTP 服务器时，使用它为局域网用户提供服务：
> server    127.127.1.0     # local clock
> fudge     127.127.1.0 stratum 10	#10:用于给局域网主机提供时间服务！
> # prefer表示为优先，表示本机优先同步该服务器时间；
> # iburst当一个运程NTP服务器不可用时，向它发送一系列的并发包进行检测；
> # prefer和iburst可以同时使用；例如：
> server times.aliyun.com iburst prefer #prefer表示为优先，表示本机优先同步该服务器时间；
> server 210.72.145.44 iburst #iburst当一个运程NTP服务器不可用时,向它发送一系列的并发包进行检测；
> server cn.pool.ntp.org iburst
> ```

# Ntp客户端ntp.conf配置

![](E:\GitStorage\bigdata\picture\20171013130835090-2027496738.png)

![](E:\GitStorage\bigdata\picture\20171013130912324-1592177452.png)

> 第一个框和第二个框可以不配置，第一个框ip地址是本地地址，是为了确保localhost有足够的权限！
>
> 第二个红框是局域网的IP地址网段。
>
> 第三个红框是向server同步时间。

# 知识点：

> 1、restrict
> 作用：对ntp做权限控制
> ignore：忽略所有类型的NTP连接请求
> nomodify：限制客户端不能使用命令ntpc和ntpq来修改服务器端的时间
> noquery：不提供NTP网络校时服务
> notrap：不接受远程登录请求
> notrust：不接受没有经过认证的客户端的请求
> 【如果没有用任何参数，那么表示不做任何限制】
> 例子：restrict 10.220.5.0 mask 255.255.255.0 nomodify <<<允许10.220.5.0/24 网段主机进行时间同步

> 2、server
> 作用：指定ntp服务器的地址
> 格式：server [ip or hostname] [perfer]
> 例子：server 127.127.1.0 <<<将当前主机作为时间服务器

> 3、fudge
> 作用：设置时间服务器的层级
> 格式：fudge ip [startnum int]
> 例子：fudge 10.225.5.1 startnum 10
> 注意：fudge必须和server一块用， 而且是在server的下一行
> startnum
> 0~15
> 0：表示顶级
> 10：通常用于给局域网主机提供时间服务

# 常用命令：

> rpm -qa | grep ntp     #查询已安装的ntp版本信息等，会查询到三个安装包；
>
> ntp-4.2.6p5-28.el7.centos.x86_64.rpm 
> ntpdate-4.2.6p5-28.el7.centos.x86_64.rpm 
> libopts-5.18.12-alt2.x86_64.rpm

> service ntpd status    #查询ntp服务状态

> service ntpd start      #启动

> service ntpd stop      #停止

> service ntpd restart   #重启

> ntpq -p   #查看[ntp服务器](https://www.baidu.com/s?wd=ntp服务器&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)与上层ntp的状态

> ntpstat     #命令查看时间同步状态，这个一般需要5-10分钟后才能成功连接和同步。所以，服务器启动后需要稍等下。

# 参考：

<https://blog.csdn.net/qq_31725371/article/details/94591903>

<https://www.cnblogs.com/quchunhui/p/7658853.html>

<https://www.cnblogs.com/kerrycode/archive/2015/08/20/4744804.html>

<https://blog.csdn.net/qq_35663625/article/details/103064495>

<https://www.cnblogs.com/magic-chenyang/p/10968488.html>