---
layout: "post"
title: "install-zookeeper"
date: "2016-10-29 15:46"
category: ["zookeeper"]
tags: [zookeeper,大数据]
---


1.首先去cdh官网下载[zookeeper](http://archive.cloudera.com/cdh5/cdh/5/zookeeper-3.4.5-cdh5.3.6.tar.gz)	

2.解压zookeeper,这里安装单机版 		

```xml

解压缩
tar -zxvf zookeeper-3.4.5-cdh5.3.6.tar.gz -C newcdh/
cd /Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/

赋值配置文件
cp conf/zoo_sample.cfg conf/zoo.cfg 


创建数据目录
mkdir -p data/zookeeper

```

3.配置zoo.cfg文件的数据目录,修改后如下所示 	

```xml

# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/data/zookeeper
# the port at which the clients will connect
clientPort=2181
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1


```

4.启动zookeeper即可    


```xml

./bin/zkServer.sh start

JMX enabled by default
Using config: /Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```

验证是否启动成功	

```xml

./bin/zkServer.sh status
JMX enabled by default
Using config: /Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../conf/zoo.cfg
Mode: standalone

```

或者通过zkCli.sh客户端验证 	

```xml

./bin/zkCli.sh 
Connecting to localhost:2181
2016-10-29 15:57:47,450 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.5-cdh5.3.6--1, built on 07/28/2015 22:11 GMT
2016-10-29 15:57:47,453 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=mac.cn
2016-10-29 15:57:47,453 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.8.0_91
2016-10-29 15:57:47,455 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Oracle Corporation
2016-10-29 15:57:47,455 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home/jre
2016-10-29 15:57:47,455 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../build/classes:/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../build/lib/*.jar:/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../share/zookeeper/zookeeper-3.4.5-cdh5.3.6.jar:/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../share/zookeeper/slf4j-log4j12-1.7.5.jar:/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../share/zookeeper/slf4j-api-1.7.5.jar:/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../share/zookeeper/netty-3.2.2.Final.jar:/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../share/zookeeper/log4j-1.2.16.jar:/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../share/zookeeper/jline-0.9.94.jar:/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../src/java/lib/*.jar:/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../conf:
2016-10-29 15:57:47,456 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/Users/mac/Library/Java/Extensions:/Library/Java/Extensions:/Network/Library/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:.
2016-10-29 15:57:47,456 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/var/folders/0s/bccg6xrx6f31mql9grggj1mr0000gn/T/
2016-10-29 15:57:47,456 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=<NA>
2016-10-29 15:57:47,456 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Mac OS X
2016-10-29 15:57:47,456 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=x86_64
2016-10-29 15:57:47,456 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=10.12.1
2016-10-29 15:57:47,456 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=mac
2016-10-29 15:57:47,457 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/Users/mac
2016-10-29 15:57:47,457 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6
2016-10-29 15:57:47,458 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=localhost:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@67424e82
Welcome to ZooKeeper!
2016-10-29 15:57:47,487 [myid:] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@975] - Opening socket connection to server localhost/0:0:0:0:0:0:0:1:2181. Will not attempt to authenticate using SASL (unknown error)
JLine support is enabled
2016-10-29 15:57:47,577 [myid:] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@852] - Socket connection established, initiating session, client: /0:0:0:0:0:0:0:1:57346, server: localhost/0:0:0:0:0:0:0:1:2181
[zk: localhost:2181(CONNECTING) 0] 2016-10-29 15:57:47,620 [myid:] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@1235] - Session establishment complete on server localhost/0:0:0:0:0:0:0:1:2181, sessionid = 0x1580f7083be0000, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null

[zk: localhost:2181(CONNECTED) 0] ls /
[zookeeper]
[zk: localhost:2181(CONNECTED) 1] ls /zookeeper
[quota]
[zk: localhost:2181(CONNECTED) 2] 


```