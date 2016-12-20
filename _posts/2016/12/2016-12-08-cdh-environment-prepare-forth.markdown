---
layout: "post"
title: "cdh环境搭建(四)安装cdh的相关服务"
date: "2016-12-08 21:52"
category: "hadoop"
tags: [大数据,虚拟机,cdh,cloudera]

---



#### cdh环境搭建(四)安装cdh的相关服务		


1.添加zookeeper服务 	

![](../assets/2016/12/2016-12-18_23-06-50.png)

![](../assets/2016/12/2016-12-18_23-07-33.png)

![](../assets/2016/12/2016-12-18_23-10-23.png)

这里选择三台主机作为zookeeper的集群 	

![](../assets/2016/12/2016-12-18_23-11-06.png)

![](../assets/2016/12/2016-12-18_23-11-39.png)

![](../assets/2016/12/2016-12-18_23-13-03.png)


![](../assets/2016/12/2016-12-18_23-13-29.png)

![](../assets/2016/12/2016-12-18_23-13-54.png)


![](../assets/2016/12/2016-12-18_23-21-03.png)

启动命令测试  	

```xml

 cd /opt/cloudera/parcels/CDH/bin/
[cdh@centos3 bin]$ ./zookeeper-client 

Connecting to localhost:2181
2016-12-18 23:24:13,680 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.5-cdh5.3.6--1, built on 07/28/2015 22:12 GMT
2016-12-18 23:24:13,686 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=centos3.com
2016-12-18 23:24:13,686 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.7.0_67
2016-12-18 23:24:13,688 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Oracle Corporation
2016-12-18 23:24:13,688 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/usr/java/jdk1.7.0_67-cloudera/jre
2016-12-18 23:24:13,688 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../build/classes:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../build/lib/*.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../lib/slf4j-log4j12.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../lib/slf4j-log4j12-1.7.5.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../lib/slf4j-api-1.7.5.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../lib/netty-3.2.2.Final.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../lib/log4j-1.2.16.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../lib/jline-0.9.94.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../zookeeper-3.4.5-cdh5.3.6.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/lib/zookeeper/bin/../src/java/lib/*.jar:/etc/zookeeper/conf::/etc/zookeeper/conf:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/bin/../lib/zookeeper/zookeeper.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/bin/../lib/zookeeper/zookeeper-3.4.5-cdh5.3.6.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/bin/../lib/zookeeper/lib/netty-3.2.2.Final.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/bin/../lib/zookeeper/lib/log4j-1.2.16.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/bin/../lib/zookeeper/lib/slf4j-log4j12.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/bin/../lib/zookeeper/lib/slf4j-log4j12-1.7.5.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/bin/../lib/zookeeper/lib/slf4j-api-1.7.5.jar:/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/bin/../lib/zookeeper/lib/jline-0.9.94.jar
2016-12-18 23:24:13,689 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
2016-12-18 23:24:13,689 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/tmp
2016-12-18 23:24:13,689 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=<NA>
2016-12-18 23:24:13,689 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Linux
2016-12-18 23:24:13,689 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=amd64
2016-12-18 23:24:13,689 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=2.6.32-358.el6.x86_64
2016-12-18 23:24:13,689 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=cdh
2016-12-18 23:24:13,690 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/home/cdh
2016-12-18 23:24:13,690 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/opt/cloudera/parcels/CDH-5.3.6-1.cdh5.3.6.p0.11/bin
2016-12-18 23:24:13,691 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=localhost:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@52dce479
Welcome to ZooKeeper!
2016-12-18 23:24:13,747 [myid:] - INFO  [main-SendThread(localhost.localdomain:2181):ClientCnxn$SendThread@975] - Opening socket connection to server localhost.localdomain/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
2016-12-18 23:24:13,759 [myid:] - INFO  [main-SendThread(localhost.localdomain:2181):ClientCnxn$SendThread@852] - Socket connection established, initiating session, client: /127.0.0.1:46301, server: localhost.localdomain/127.0.0.1:2181
2016-12-18 23:24:13,777 [myid:] - INFO  [main-SendThread(localhost.localdomain:2181):ClientCnxn$SendThread@1235] - Session establishment complete on server localhost.localdomain/127.0.0.1:2181, sessionid = 0x1591283a7e30005, negotiated timeout = 30000
JLine support is enabled

WATCHER::

WatchedEvent state:SyncConnected type:None path:null

create /test 'test-data'
Created /test
[zk: localhost:2181(CONNECTED) 4] ls /
[test, zookeeper]
[zk: localhost:2181(CONNECTED) 5] ls /test/

```


2.安装hdfs 		

![](../assets/2016/12/2016-12-19_19-36-06.png)

![](../assets/2016/12/2016-12-19_19-36-44.png)

![](../assets/2016/12/2016-12-19_19-37-26.png)

![](../assets/2016/12/2016-12-19_19-38-47.png)

![](../assets/2016/12/2016-12-19_19-39-43.png)

![](../assets/2016/12/2016-12-19_19-43-24.png)

![](../assets/2016/12/2016-12-19_19-44-06.png)

3.安装yarn  		

![](../assets/2016/12/2016-12-19_19-59-28.png)

![](../assets/2016/12/2016-12-19_20-00-01.png)

![](../assets/2016/12/2016-12-19_20-00-27.png)

![](../assets/2016/12/2016-12-19_20-01-16.png)

![](../assets/2016/12/2016-12-19_20-02-02.png)

![](../assets/2016/12/2016-12-19_20-11-51.png)

![](../assets/2016/12/2016-12-19_20-12-32.png)

4.安装hive   	

![](../assets/2016/12/2016-12-19_20-17-43.png)

![](../assets/2016/12/2016-12-19_20-18-34.png)

![](../assets/2016/12/2016-12-19_20-19-05.png)

![](../assets/2016/12/2016-12-19_20-20-12.png)

![](../assets/2106/12/2016-12-19_21-00-06.png)

![](../assets/2106/12/2016-12-19_21-00-50.png)

![](../assets/2106/12/2016-12-19_21-25-26.png)

![](../assets/2016/12/2016-12-19_21-26-05.png)





