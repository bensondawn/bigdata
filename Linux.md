1. #### data

> date "+%Y-%m-%d %H:%M:%S" -d @1494570129	//把秒转换为自己想要的时间格式
>
> date --date='2017-04-28 11:43:11' +%s		//把时间转换为秒

2. #### Linux安装文件对比工具

> apt-get install meld	运行use/bin/meld即可！

3. #### 查看linux内核版本

> [root@q1test01 ~]# cat /proc/version 
>
> Linux version 2.6.9-22.ELsmp (bhcompile@crowe.devel.redhat.com) (gcc version 3.4.4 20050721 (Red Hat 3.4.4-2)) #1 SMP Mon Sep 19 18:00:54 EDT 2005 

> [root@q1test01 ~]# uname -a 
> Linux q1test01 2.6.9-22.ELsmp #1 SMP Mon Sep 19 18:00:54 EDT 2005 x86_64 x86_64 x86_64 GNU/Linux 

> [root@q1test01 ~]# uname -r 
> 2.6.9-22.ELsmp 

4. #### 查看Linux版本

> zengdx@ubuntu:~$ lsb_release -a
> No LSB modules are available.
> Distributor ID: Ubuntu
> Description:    Ubuntu 14.04.3 LTS
> Release:        14.04
> Codename:       trusty

> zengdx@ubuntu:~$ cat /etc/lsb-release
> DISTRIB_ID=Ubuntu
> DISTRIB_RELEASE=14.04
> DISTRIB_CODENAME=trusty
> DISTRIB_DESCRIPTION="Ubuntu 14.04.3 LTS"

> zengdx@ubuntu:~$ cat /etc/issue
> Ubuntu 14.04.3 LTS

5. #### 清空文件

> cat /dev/null > access.log	//清空access.log

6. #### ubuntu中查看所有的用户信息

> cat /etc/passwd

7. #### chown命令

> 命令格式：chown [选项]... [所有者][:[组]] 文件...

> 命令功能：通过chown改变文件的拥有者和群组。在更改文件的所有者或所属群组时，可以使用用户名称和用户识别码设置。普通用户不能将自己的文件改变成其他的拥有者。其操作权限一般为管理员。

> 实例：
>
> - 改变拥有者和群组
>
> 　　chown mail:mail log2012.log
>
> -  改变文件拥有者
>
> 　　chown root: log2012.log
>
> - 改变文件群组
>
> 　　chown :mail log2012.log
>
> - 改变指定目录以及其子目录下的所有文件的拥有者和群组
>
> 　　chown -R root:mail test6

8. #### 防火墙

> 1）安装
> sudo apt-get install ufw
> 2）启用
> sudo ufw enable
> sudo ufw default deny
> 运行以上两条命令后，开启了防火墙，并在系统启动时自动开启。关闭所有外部对本机的访问，但本机访问外部正常。
> 3）关闭
> sudo ufw disable 
> 4）查看防火墙的状态
> sudo ufw status
> 5）开启/禁用
> sudo ufw allow|deny [service]
> 打开或关闭某个端口，例如：
> sudo ufw allow smtp　允许所有的外部IP访问本机的25/tcp (smtp)端口
> sudo ufw allow 22/tcp 允许所有的外部IP访问本机的22/tcp (ssh)端口
> sudo ufw allow 53 允许外部访问53端口(tcp/udp)
> sudo ufw allow from 192.168.1.100 允许此IP访问所有的本机端口
> sudo ufw allow proto udp 192.168.0.1 port 53 to 192.168.0.2 port 53
> sudo ufw deny smtp 禁止外部访问smtp服务
> sudo ufw delete allow smtp 删除上面建立的某条规则

9. #### killall命令

> killall 参数 进程名称(全称，不能仅包含一部分名称)
> 参数：
> -Z 只杀死拥有scontext 的进程
> -e 要求匹配进程名称(当进程名大于15个字符时有用http://os.51cto.com/art/201405/437678.htm)
> -I 忽略小写
> -g 杀死进程组而不是进程
> -i 交互模式，杀死进程前先询问用户
> -l 列出所有的已知信号名称
> -q 不输出警告信息
> -s 发送指定的信号
> -v 报告信号是否成功发送
> -w 等待进程死亡
> --help 显示帮助信息
> --version 显示版本显示
> 实例：
> killall vi	//杀死所有用vi编辑文件的进程；
> killall test test_again	//直接杀死多个进程；
> killall -I VI	//杀死所有用vi编辑文件的进程，忽略vi的大小写；
> killall -i test test_again	// 使用 -i 选项交互式的终止进程，可以同时杀死多个进程，并且有选项是否杀死某一进程；
> killall -9 bash	//把所有登陆后的shell都杀死，所有当前连接都断开了，需要重新连接并登录；

10. #### ssh命令

> 不指定用户：ssh 172.16.0.133
> 指定用户：ssh root@172.16.0.133 或者 ssh -I root 172.16.0.133
> 指定端口：ssh -p 7070 172.16.0.133
> 指定用户和端口：ssh -I root -p 7070 172.16.0.133 
> 或者 ssh -p 7070 root@172.16.0.133
> 修改配置文件/etc/ssh/sshd_config，可以改ssh登录端口和禁止root登录；改端口可以防止被端口扫描。
> 编辑配置文件：
> vim /etc/ssh/sshd_config
> 找到#Port 22，去掉注释，修改成一个五位的端口：
> Port 12333
> 找到#PermitRootLogin yes，去掉注释，修改为：
> PermitRootLogin no 禁止root登录；
> 重启sshd服务：
> service sshd restart

11. #### df命令

> df命令列出文件系统的整体磁盘使用情况，剩余空间情况，是基于文件系统总体来计算，获取硬盘占用多少空间，还剩余多少空间。
>
> 语法：df [-ahikHTm] [目录/文件名]
>
> 参数：
>
> -a：列出所有文件系统，包括系统特有的/proc等文件系统；
>
> -k：以KBytes单位显示；
>
> -m：以MBytes单位显示；
>
> -h：以人们易读的GB、MB、KB等单位显示；
>
> -H：以M=1000KB取代1024KB的进位方式；
>
> -T：显示文件系统类型；
>
> -i：不用硬盘容量，以inode的数量来显示；
>
> 案例：
>
> df -h：以更易读的方式显示目前磁盘使用和剩余情况；(大文件占用大量磁盘空间)
>
> df -i：以inode模式来显示磁盘使用情况；(过多的小文件占用了大量的inode号，每个小文件都包含文件的字节数、拥有者id、组id、权限、改动时间、链接数、数据block位置)

12. #### du命令

> du命令用于显示指定目录或文件所占用的磁盘空间。该命令是面向文件的命令，只计算被文件占用的空间，不计算文件系统占用的空间。
>
> 语法：du [-ahskm] 文件/目录名称
>
> 参数：
>
> -a：显示全部文件，包括隐藏文件；
>
> -h：以K、M、G格式显示，提高信息的可读性；
>
> -s：仅显示总计，而不列出目录下的各个文件的占用量；
>
> -S：显示目录大小时，不包括子目录大小；
>
> -k：以KB为单位显示；
>
> -m：以MB为单位显示；
>
> 实例：
>
> du -sh .	//以方便的方式显示当前目录的占用情况；
>
> du -sh * | sort -h	//从小到大排序
>
> du -sh * | sort -rh	//从大到小排序

13. #### free命令

> free命令用于显示内存使用情况。
>
> 语法：free [-bkmotV] [-s <间隔秒数>]
>
> free -th -s [间隔秒数]	//不加-s 秒数，只显示当前的；加上-s 秒数会每隔几秒刷新一次。
>
> 案例：
>
> free -h	//以较友好的方式显示。
>
> free -l	//显示详细的低内存和高内存统计信息。

14. #### fdisk命令

> fdisk是分区工具。
>
> fdisk -l	//查看硬盘及硬盘下的分区，包括没有挂上的分区和usb设备。
>

15. #### 查看cpu型号

>lscpu

16. #### find命令

> 语法：find [path] [options] [-print/-ecex/-ok]
>
> - path：要查找的路径；(~表示home目录/-print表示将结果数据在标准输出上/-ecex表示对文件执行所给出的shell命令)
>
> - options：
>   - -name：按照名称查找；
>   - -perm：按照权限查找；
>   - -user：按照文件属主查找；
>
> 案例：
>
> - find . -name '[a-z]*.c' -print	//查找小写字母开头的.c文件
> - find ~ -name '*' -print       //查找home目录及其子目录中所有文件
> - find /etc/ -name *.conf    //查找/etc/目录下名称中带.conf的文件
> - find /etc/ -name password //查找/etc/目录下名称中带password的文件

17. #### top命令

> top命令将当前系统的运行状况最直观的告诉使用者。