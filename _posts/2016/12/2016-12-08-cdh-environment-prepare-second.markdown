---
layout: "post"
title: "cdh环境搭建(二)yum离线安装cm"
date: "2016-12-08 08:52"
category: "hadoop"
tags: [大数据,虚拟机,cdh,cloudera]

---



#### cdh环境搭建(二)软件安装		

##### CM软件安装,这里使用5.3.6版本 			

1.下载bin文件，[http://archive.cloudera.com/cm5/installer/5.3.6/ ](http://archive.cloudera.com/cm5/installer/5.3.6/),一个文件 		

2.下载rpm相关依赖包，[http://archive.cloudera.com/cm5/redhat/6/x86_64/cm/5.3.6/RPMS/x86_64/](http://archive.cloudera.com/cm5/redhat/6/x86_64/cm/5.3.6/RPMS/x86_64/),总共7个文件 		  	

下载完成后目录结构如下所示  		

![下载文件列表](../assets/2016/12/2016-12-07_21-47-42.png)	

3.下载依赖包，数据库postgresq及repo-libs				

4.搭建本地yum源 			

4.1安装repo-libs软件包 		

```xml

sudo rpm -ivh ./*.rpm
warning: ./createrepo-0.9.9-18.el6.noarch.rpm: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
Preparing...                ########################################### [100%]
   1:deltarpm               ########################################### [ 33%]
   2:python-deltarpm        ########################################### [ 67%]
   3:createrepo             ########################################### [100%]


```

4.2启动Apache服务器  		

```xml

[cdh@centos Desktop]$ rpm -qa | grep httpd
httpd-tools-2.2.15-26.el6.centos.x86_64
httpd-2.2.15-26.el6.centos.x86_64
[cdh@centos Desktop]$ service httpd status
httpd is stopped
[cdh@centos Desktop]$ sudo service httpd start
[sudo] password for cdh: 
Starting httpd:                                            [  OK  ]
[cdh@centos Desktop]$ sudo chkconfig httpd on
[cdh@centos Desktop]$ sudo chkconfig --list | grep httpd
httpd          	0:off	1:off	2:on	3:on	4:on	5:on	6:off

```

4.3在apache网站目录下创建新的目录，用来存放软件  		

```xml

cd /var/www/html/

[cdh@centos html]$ pwd
/var/www/html
[cdh@centos html]$ sudo mkdir -p cm5/redhat/6/x86_64/cm/5.3.6/RPMS/x86_64/


```

访问centos中的ip地址可以看到开启的apache服务器,http://192.168.4.129/cm5/redhat/6/x86_64/cm/5.3.6/RPMS/x86_64/		  		

![](../assets/2016/12/2016-12-07_22-46-38.png)


4.4上传文件到上面的新目录下,这里使用scp拷贝，也可以使用上传工具  	

```xml


[cdh@centos html]$ sudo service sshd start
Generating SSH1 RSA host key:                              [  OK  ]
Generating SSH2 RSA host key:                              [  OK  ]
Generating SSH2 DSA host key:                              [  OK  ]
Starting sshd:                                             [  OK  ]
[cdh@centos html]$ sudo chkconfig sshd on
[cdh@centos html]$ sudo chkconfig --list | grep sshd
sshd           	0:off	1:off	2:on	3:on	4:on	5:on	6:off


```  	

将cm软件先上传到centos根目录下  	

```xml

 scp software/bigdata/cloudera/cm-5.3.6/* cdh@192.168.4.129:~/Desktop/
cdh@192.168.4.129's password: 
cloudera-manager-agent-5.3.6-1.cm536.p0.244.e 100% 3896KB   3.8MB/s   00:00    
cloudera-manager-daemons-5.3.6-1.cm536.p0.244 100%  476MB  36.6MB/s   00:13    
cloudera-manager-installer.bin                100%  502KB 502.2KB/s   00:00    
cloudera-manager-server-5.3.6-1.cm536.p0.244. 100% 7852     7.7KB/s   00:00    
cloudera-manager-server-db-2-5.3.6-1.cm536.p0 100% 9884     9.7KB/s   00:00    
enterprise-debuginfo-5.3.6-1.cm536.p0.244.el6 100%  677KB 676.8KB/s   00:00    
jdk-6u31-linux-amd64.rpm                      100%   68MB  34.0MB/s   00:02    
oracle-j2sdk1.7-1.7.0+update67-1.x86_64.rpm   100%  135MB 135.5MB/s   00:01  

```

将上传到cenos桌面上的rpm软件包全部放到apache新建的目录下   	

```xml

cd cm5/redhat/6/x86_64/cm/5.3.6/RPMS/x86_64/
[cdh@centos x86_64]$ ls
[cdh@centos x86_64]$ sudo mv ~/Desktop/*.rpm ./
[cdh@centos x86_64]$ ls -al
total 700576
drwxr-xr-x 2 root root      4096 Dec  7 22:54 .
drwxr-xr-x 3 root root      4096 Dec  7 22:44 ..
-rwxrwxr-x 1 cdh  cdh    3989520 Dec  7 22:53 cloudera-manager-agent-5.3.6-1.cm536.p0.244.el6.x86_64.rpm
-rwxrwxr-x 1 cdh  cdh  499418684 Dec  7 22:53 cloudera-manager-daemons-5.3.6-1.cm536.p0.244.el6.x86_64.rpm
-rwxrwxr-x 1 cdh  cdh       7852 Dec  7 22:53 cloudera-manager-server-5.3.6-1.cm536.p0.244.el6.x86_64.rpm
-rwxrwxr-x 1 cdh  cdh       9884 Dec  7 22:53 cloudera-manager-server-db-2-5.3.6-1.cm536.p0.244.el6.x86_64.rpm
-rwxrwxr-x 1 cdh  cdh     693024 Dec  7 22:53 enterprise-debuginfo-5.3.6-1.cm536.p0.244.el6.x86_64.rpm
-rwxrwxr-x 1 cdh  cdh   71204325 Dec  7 22:53 jdk-6u31-linux-amd64.rpm
-rwxrwxr-x 1 cdh  cdh  142039186 Dec  7 22:53 oracle-j2sdk1.7-1.7.0+update67-1.x86_64.rpm


```


继续访问页面查看是否成功   	


![](../assets/2016/12/2016-12-07_22-56-04.png)

在/var/www/html/cm5/redhat/6/x86_64/cm/5目录下执行命令 	  		

```xml
cd /var/www/html/cm5/redhat/6/x86_64/cm/5

[cdh@centos2 5]$ sudo createrepo .
Spawning worker 0 with 6 pkgs
Workers Finished
Gathering worker results

Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete

```


4.5新建yum源文件cloudera-manager.repo     	


```xml

cd /etc/yum.repos.d/
[cdh@centos yum.repos.d]$ pwd
/etc/yum.repos.d   		


```

这里将所有的repo源先移走或者直接删掉  	

sudo mv CentOS-* ~/Desktop/

新建repo源	

sudo vi cloudera-manager.repo

添加下面内容 		


```xml

[cloudera-manager]
# Packages for Cloudera Manager, Version 5, on RedHat or CentOS 6 x86_64                  
name=Cloudera Manager
baseurl=http://192.168.4.129/cm5/redhat/6/x86_64/cm/5.3.6/
gpgkey =https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/RPM-GPG-KEY-cloudera    
gpgcheck = 0          

```

4.6安装postgresq  		

上传postgresq到centos中的cdh用户目录下的software下  		

```xml

mac:cloudera mac$ scp -r postgresq-libs/ cdh@192.168.4.129:/home/cdh/software
postgresql-8.4.18-1.el6_4.x86_64.rpm          100% 2838KB   2.8MB/s   00:00    
postgresql-libs-8.4.18-1.el6_4.x86_64.rpm     100%  201KB 200.9KB/s   00:00    
postgresql-server-8.4.18-1.el6_4.x86_64.rpm   100% 3518KB   3.4MB/s   00:00  

```

安装postgresql		

```xml

sudo rpm -ivh *.rpm
warning: postgresql-8.4.18-1.el6_4.x86_64.rpm: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
Preparing...                ########################################### [100%]
   1:postgresql-libs        ########################################### [ 33%]
   2:postgresql             ########################################### [ 67%]
   3:postgresql-server      ########################################### [100%]

```		

4.7修改本机的ip地址与域名绑定,将archive.cloudera.com域名绑定本地ip，这样就能通过本地搭建的80服务器httpd服务器访问下载了，如果不添加该步骤，则总是联网下载			


```xml

sudo vi /etc/hosts

192.168.4.130   archive.cloudera.com


```


4.8执行cloudera-manager-installer.bin文件  	

必须要用root权限  	


```xml

sudo ./cloudera-manager-installer.bin

```

![](../assets/2016/12/2016-12-08_19-55-41_01.png)

![](../assets/2016/12/2016-12-08_19-56-35.png)

![](../assets/2016/12/2016-12-08_19-57-09.png)

![](../assets/2016/12/2016-12-08_19-57-40.png)

![](../assets/2016/12/2016-12-08_19-58-20.png)

![](../assets/2016/12/2016-12-08_21-10-48.png)


4.9执行完毕后会看到提示信息，访问7180端口看到下面界面  	

![](../assets/2016/12/2016-12-18_17-28-01.png)

用admin/admin登录后看到页面 	

![](../assets/2016/12/2016-12-18_17-28-55.png)

选择免费版本后，继续 		

![](../assets/2016/12/2016-12-18_17-29-57.png)

