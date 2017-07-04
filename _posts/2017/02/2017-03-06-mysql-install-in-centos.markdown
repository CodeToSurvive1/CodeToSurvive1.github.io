---
layout: "post"
title: "centos mysql5.6.24安装 "
date: "2017-03-06 20:52"
category: "other"
tags: [mysql]

---


#### 先决条件

下载centos系统版本mysql程序，包括server端，client端，以及java 驱动程序，下载地址，[http://pan.baidu.com/s/1nvvS7PZ](http://pan.baidu.com/s/1nvvS7PZ)		

------

#### 安装步骤

1.首先查看系统中有没有自带的mysql安装程序    

```
rpm -qa | grep mysql
qt-mysql-4.6.2-25.el6.x86_64
mysql-server-5.1.66-2.el6_3.x86_64
mysql-libs-5.1.66-2.el6_3.x86_64
mysql-5.1.66-2.el6_3.x86_64
```

2.如果有，首先卸载mysql程序     

```
[mac@centos1 ~]$ sudo rpm -e --nodeps qt-mysql-4.6.2-25.el6.x86_64
[sudo] password for mac: 
[mac@centos1 ~]$ sudo rpm -e --nodeps mysql-server-5.1.66-2.el6_3.x86_64
[mac@centos1 ~]$ sudo rpm -e --nodeps mysql-libs-5.1.66-2.el6_3.x86_64
[mac@centos1 ~]$ sudo rpm -e --nodeps mysql-5.1.66-2.el6_3.x86_64
[mac@centos1 ~]$ 

```

3.查看是否卸载成功    

```
[mac@centos1 ~]$ rpm -qa| grep mysql

```

4.将mysql 安装程序拷贝到centos系统中的software目录下，并解压切换到software目录下安装      
```
mac:~ mac$ scp ~/Downloads/mysql-libs.zip centos1:/opt/software
mac@centos1's password: 
mysql-libs.zip                                100%   74MB  36.3MB/s   00:02

[mac@centos1 software]$ unzip mysql-libs.zip 
Archive:  mysql-libs.zip
   creating: mysql-libs/
  inflating: mysql-libs/MySQL-client-5.6.24-1.el6.x86_64.rpm  
  inflating: mysql-libs/mysql-connector-java-5.1.27.tar.gz  
  inflating: mysql-libs/MySQL-server-5.6.24-1.el6.x86_64.rpm 

[mac@centos1 mysql-libs]$ ls
MySQL-client-5.6.24-1.el6.x86_64.rpm  MySQL-server-5.6.24-1.el6.x86_64.rpm
mysql-connector-java-5.1.27.tar.gz
[mac@centos1 mysql-libs]$ sudo rpm -ivh *.rpm
[sudo] password for mac: 
Preparing...                ########################################### [100%]
   1:MySQL-server           ########################################### [ 50%]
2017-03-05 11:37:29 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2017-03-05 11:37:29 0 [Note] /usr/sbin/mysqld (mysqld 5.6.24) starting as process 2195 ...
2017-03-05 11:37:29 2195 [Note] InnoDB: Using atomics to ref count buffer pool pages
2017-03-05 11:37:29 2195 [Note] InnoDB: The InnoDB memory heap is disabled
2017-03-05 11:37:29 2195 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
2017-03-05 11:37:29 2195 [Note] InnoDB: Memory barrier is not used
2017-03-05 11:37:29 2195 [Note] InnoDB: Compressed tables use zlib 1.2.3
2017-03-05 11:37:29 2195 [Note] InnoDB: Using Linux native AIO
2017-03-05 11:37:29 2195 [Note] InnoDB: Using CPU crc32 instructions
2017-03-05 11:37:29 2195 [Note] InnoDB: Initializing buffer pool, size = 128.0M
2017-03-05 11:37:29 2195 [Note] InnoDB: Completed initialization of buffer pool
2017-03-05 11:37:29 2195 [Note] InnoDB: The first specified data file ./ibdata1 did not exist: a new database to be created!
2017-03-05 11:37:29 2195 [Note] InnoDB: Setting file ./ibdata1 size to 12 MB
2017-03-05 11:37:29 2195 [Note] InnoDB: Database physically writes the file full: wait...
2017-03-05 11:37:29 2195 [Note] InnoDB: Setting log file ./ib_logfile101 size to 48 MB
2017-03-05 11:37:29 2195 [Note] InnoDB: Setting log file ./ib_logfile1 size to 48 MB
2017-03-05 11:37:30 2195 [Note] InnoDB: Renaming log file ./ib_logfile101 to ./ib_logfile0
2017-03-05 11:37:30 2195 [Warning] InnoDB: New log files created, LSN=45781
2017-03-05 11:37:30 2195 [Note] InnoDB: Doublewrite buffer not found: creating new
2017-03-05 11:37:30 2195 [Note] InnoDB: Doublewrite buffer created
2017-03-05 11:37:30 2195 [Note] InnoDB: 128 rollback segment(s) are active.
2017-03-05 11:37:30 2195 [Warning] InnoDB: Creating foreign key constraint system tables.
2017-03-05 11:37:30 2195 [Note] InnoDB: Foreign key constraint system tables created
2017-03-05 11:37:30 2195 [Note] InnoDB: Creating tablespace and datafile system tables.
2017-03-05 11:37:30 2195 [Note] InnoDB: Tablespace and datafile system tables created.
2017-03-05 11:37:30 2195 [Note] InnoDB: Waiting for purge to start
2017-03-05 11:37:30 2195 [Note] InnoDB: 5.6.24 started; log sequence number 0
A random root password has been set. You will find it in '/root/.mysql_secret'.
2017-03-05 11:37:30 2195 [Note] Binlog end
2017-03-05 11:37:30 2195 [Note] InnoDB: FTS optimize thread exiting.
2017-03-05 11:37:30 2195 [Note] InnoDB: Starting shutdown...
2017-03-05 11:37:32 2195 [Note] InnoDB: Shutdown completed; log sequence number 1625977


2017-03-05 11:37:32 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2017-03-05 11:37:32 0 [Note] /usr/sbin/mysqld (mysqld 5.6.24) starting as process 2217 ...
2017-03-05 11:37:32 2217 [Note] InnoDB: Using atomics to ref count buffer pool pages
2017-03-05 11:37:32 2217 [Note] InnoDB: The InnoDB memory heap is disabled
2017-03-05 11:37:32 2217 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
2017-03-05 11:37:32 2217 [Note] InnoDB: Memory barrier is not used
2017-03-05 11:37:32 2217 [Note] InnoDB: Compressed tables use zlib 1.2.3
2017-03-05 11:37:32 2217 [Note] InnoDB: Using Linux native AIO
2017-03-05 11:37:32 2217 [Note] InnoDB: Using CPU crc32 instructions
2017-03-05 11:37:32 2217 [Note] InnoDB: Initializing buffer pool, size = 128.0M
2017-03-05 11:37:32 2217 [Note] InnoDB: Completed initialization of buffer pool
2017-03-05 11:37:32 2217 [Note] InnoDB: Highest supported file format is Barracuda.
2017-03-05 11:37:32 2217 [Note] InnoDB: 128 rollback segment(s) are active.
2017-03-05 11:37:32 2217 [Note] InnoDB: Waiting for purge to start
2017-03-05 11:37:32 2217 [Note] InnoDB: 5.6.24 started; log sequence number 1625977
2017-03-05 11:37:32 2217 [Note] Binlog end
2017-03-05 11:37:32 2217 [Note] InnoDB: FTS optimize thread exiting.
2017-03-05 11:37:32 2217 [Note] InnoDB: Starting shutdown...
2017-03-05 11:37:34 2217 [Note] InnoDB: Shutdown completed; log sequence number 1625987




A RANDOM PASSWORD HAS BEEN SET FOR THE MySQL root USER !
You will find that password in '/root/.mysql_secret'.

You must change that password on your first connect,
no other statement but 'SET PASSWORD' will be accepted.
See the manual for the semantics of the 'password expired' flag.

Also, the account for the anonymous user has been removed.

In addition, you can run:

  /usr/bin/mysql_secure_installation

which will also give you the option of removing the test database.
This is strongly recommended for production servers.

See the manual for more instructions.

Please report any problems at http://bugs.mysql.com/

The latest information about MySQL is available on the web at

  http://www.mysql.com

Support MySQL by buying support/licenses at http://shop.mysql.com

New default config file was created as /usr/my.cnf and
will be used by default by the server when you start it.
You may edit this file to change server settings

   2:MySQL-client           ########################################### [100%]
```

5.安装mysql 服务端，安装完成后注意提示信息，密码自动放在/root/.mysql_secret文件中，并且需要在第一次连接的时候通过set password来修改密码   

```
[mac@centos1 mysql-libs]$ su
密码：
[root@centos1 mysql-libs]# cat /root/.mysql_secret
# The random password set for the root user at Sun Mar  5 11:37:30 2017 (local time): qr2xuNRQnrh48eZA

[root@centos1 mysql-libs]# service mysql start
Starting MySQL.                                            [  OK  ]
[root@centos1 mysql-libs]# 

[root@centos1 mysql-libs]# mysql -uroot -pqr2xuNRQnrh48eZA
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.24

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```

6.mysql连接成功后通过set password修改密码,然后退出重新登录，查询数据库验证是否修改成功       

```
mysql> set password=password('123456');
Query OK, 0 rows affected (0.00 sec)

mysql> exit
Bye
[root@centos1 mysql-libs]# mysql -uroot -p123456
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.6.24 MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)


```

7.查看user表中的数据，显示了用户名对应的登陆主机设置,然后修改设置为远程ip都可以访问.  

```
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed


mysql> select host,user,password from user;
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| localhost | root | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
| centos1   | root | *E0781EC3BF7F5F4B9DCA25B2D84C259741AEE1E5 |
| 127.0.0.1 | root | *E0781EC3BF7F5F4B9DCA25B2D84C259741AEE1E5 |
| ::1       | root | *E0781EC3BF7F5F4B9DCA25B2D84C259741AEE1E5 |
+-----------+------+-------------------------------------------+
4 rows in set (0.00 sec)

mysql>update user set host='%' where user='root' and host='localhost';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

最后不要忘记重启mysql 

sudo service mysql restart

``` 

 


