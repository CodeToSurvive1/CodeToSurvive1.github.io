---
layout: "post"
title: "cdh环境搭建(三)yum离线安装cdh"
date: "2016-12-08 21:52"
category: "hadoop"
tags: [大数据,虚拟机,cdh,cloudera]

---



#### cdh环境搭建(三)yum离线安装cdh			

1.下载cdh软件包,[链接](http://archive.cloudera.com/cdh5/parcels/5.3.6/) 		


```xml

http://archive.cloudera.com/cdh5/parcels/5.3.6/CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel
http://archive.cloudera.com/cdh5/parcels/5.3.6/CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.sha1

```

2.选择管理->设置

![](../assets/2106/12/2016-12-18_19-25-33.png)

![](../assets/2016/12/2016-12-18_19-26-49.png)

![](../assets/2106/12/2016-12-18_19-27-33.png)

3.将1中下载的parcel文件放到目录下/opt/cloudera/parcel-repo 	 	

```xml

sudo mv * /opt/cloudera/parcel-repo/

 cd /opt/cloudera/parcel-repo/
[cdh@centos2 parcel-repo]$ ll
总用量 1473856
-rwxrwxr-x 1 cdh cdh 1509217191 12月  8 20:54 CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.parcel
-rwxrwxr-x 1 cdh cdh         41 12月  8 20:54 CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.sha1
[cdh@centos2 parcel-repo]$ 


```

4.将CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.sha1该名为CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.sha  		

```xml

sudo mv CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.sha1 CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.sha
[cdh@centos2 parcel-repo]$ ll
总用量 1473856
-rwxrwxr-x 1 cdh cdh 1509217191 12月  8 20:54 CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.parcel
-rwxrwxr-x 1 cdh cdh         41 12月  8 20:54 CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.sha

```

5.将CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.parcel重命名，因为多了一个parcel后缀 	

```xml

sudo mv CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel.parcel CDH-5.3.6-1.cdh5.3.6.p0.11-el6.parcel

```

6.重启cm 	

```xml

sudo service cloudera-scm-server status
cloudera-scm-server (pid  5623) is running...
[cdh@centos2 parcel-repo]$ sudo service cloudera-scm-server restart
Stopping cloudera-scm-server:                              [  OK  ]
Starting cloudera-scm-server:                              [  OK  ]


```

7.修改安装cm主机上配置的yum源为本地的服务器地址,上篇文章中配置的centos2  	

```xml

 sudo vi cloudera-manager.repo

[cloudera-manager]
name=Cloudera Manager
baseurl=http://centos2.com/cm5/redhat/6/x86_64/cm/5/
gpgkey = http://centos2.com/cm5/redhat/6/x86_64/cm/RPM-GPG-KEY-cloudera
gpgcheck=1

```

8.复制该文件到集群的其他节点centos1和centos3 		

```xml

$ sudo rm -rf *
[sudo] password for cdh: 
[cdh@centos3 yum.repos.d]$ sudo scp cdh@centos2.com:/etc/yum.repos.d/cloudera-manager.repo ./
The authenticity of host 'centos2.com (192.168.4.132)' can't be established.
RSA key fingerprint is 0a:28:51:a8:23:f3:7b:84:8a:c6:82:de:84:a6:9c:bb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'centos2.com,192.168.4.132' (RSA) to the list of known hosts.
cdh@centos2.com's password: 
cloudera-manager.repo                                                                                                                              100%  177     0.2KB/s   00:00    
[cdh@centos3 yum.repos.d]$ 


```

9.配置cm中的相关配置  	

9.1添加主机  	
![](../assets/2016/12/2016-12-18_20-04-03.png)

点击搜索 	

![](../assets/2016/12/2016-12-18_20-04-49.png)

![](../assets/2016/12/2016-12-18_20-05-34.png)

9.2修改cm agent的yum的安装地址	，也就是yum源中配置的地址		

![](../assets/2016/12/2016-12-18_20-06-50.png)

![](../assets/2016/12/2016-12-18_20-07-24.png)

9.3选择sdk安装 	

![](../assets/2016/12/2016-12-18_20-07-57.png)

继续 	

![](../assets/2016/12/2016-12-18_20-08-40.png)

9.4修改用户名及密码 		

![](../assets/2016/12/2016-12-18_20-09-37.png)

9.5进入安装页面  	

![](../assets/2016/12/2016-12-18_20-10-55.png)

报错  

![](../assets/2016/12/2016-12-18_20-31-02.png)

![](../assets/2016/12/2016-12-18_20-31-27.png)

修改三台主机的sudoers文件 		

```xml

cdh     ALL=(ALL)       NOPASSWD: ALL


```

9.6重新安装  	

![](../assets/2016/12/2016-12-18_20-41-20.png)

![](../assets/2016/12/2016-12-18_21-17-33.png)

![](../assets/2016/12/2016-12-18_21-20-54.png)


![](../assets/2016/12/2016-12-18_21-22-33.png)

9.7修改相关三个错误信息  		

9.7.1	

```xml

检查 /etc/hosts 时发现以下错误... 
主机名称 centos2.com 不是 centos2.com 上的 /etc/hosts 中的地址 192.168.4.132 的第一个匹配。相反，archive.cloudera.com 是第一个匹配。FQDN 必须是相应 IP 的 /etc/hosts 中的第一个条目。
在 centos2.com 上的 /etc/hosts 中，IP 192.168.4.132 出现多次。给定 IP 应只列出一次。

```

```xml

[cdh@centos2 ~]$ sudo vi /etc/hosts


```

删除下面信息  	

```xml
192.168.4.132   archive.cloudera.com

```

9.7.2 	

```xml

Cloudera 建议将 /proc/sys/vm/swappiness 设置为 0。当前设置为 60。使用 sysctl 命令在运行时更改该设置并编辑 /etc/sysctl.conf 以在重启后保存该设置。您可以继续进行安装，但可能会遇到问题，Cloudera Manager 报告您的主机由于交换运行状况不佳。以下主机受到影响： 
centos[1-3].com

```

在三台主机上都执行下面信息 	

```xml

sudo sysctl -w vm.swappiness=0
vm.swappiness = 0

su

echo "vm.swappiness=0" >> /etc/sysctl.conf

exit


```

9.7.3	

```xml

已启用“透明大页面”，它可能会导致重大的性能问题。版本为“CentOS release 6.4 (Final)”且发行版为“2.6.32-358.el6.x86_64”的 Kernel 已将 enabled 设置为“[always] never”，并将 defrag 设置为“[always] never”。请运行“echo never > /sys/kernel/mm/redhat_transparent_hugepage/defrag”以禁用此设置，然后将同一命令添加到一个 init 脚本中，如 /etc/rc.local，这样当系统重启时就会予以设置。或者，升级到 RHEL 6.5 或更新版本，它们不存在此错误。将会影响到以下主机： 
centos[1-3].com

```

分别在三台机器上切换到root用户执行下面命令		

```xml

su

echo never > /sys/kernel/mm/redhat_transparent_hugepage/defrag

exit

```

并分别将命令添加到/etc/rc.local文件中 	

```xml

sudo vi /etc/rc.local 

echo never > /sys/kernel/mm/redhat_transparent_hugepage/defrag


```

重新检测后全部通过，点击完成	

![](../assets/2016/12/2016-12-18_21-43-28.png)

10.添加cloudera management service 	

![](../assets/2016/12/2016-12-18_21-47-06.png)

这里都设置centos2作为角色管理者		

![](../assets/2016/12/2016-12-18_21-49-31.png)

设置数据库 	

![](../assets/2016/12/2016-12-18_21-50-30.png)

![](../assetss/2016/12/2016-12-18_21-54-08.png)

注意这里的数据库主机名称端口前面要添加上主机地址，不然测试连接不通过  	
记住密码gCn0ZFl3JD		

点击继续 	

![](../assets/2016/12/2016-12-18_21-55-52.png)

![](../assets/2016/12/2016-12-18_21-56-30.png)

![](../assets/2016/12/2016-12-18_21-58-14.png)

点击详细信息可以查看启动相关进程 	

![](../assets/2016/12/2016-12-18_21-59-05.png)

安装成功 		

![](../assets/2016/12/2016-12-18_21-59-37.png)

11.查看主页面 		

![](../assets/2016/12/2016-12-18_23-02-55.png)



