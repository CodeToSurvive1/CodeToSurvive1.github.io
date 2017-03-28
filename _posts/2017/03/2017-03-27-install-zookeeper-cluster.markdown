---
layout: "post"
title: "zookeeper集群安装"
date: "2017-03-27 22:17"
category: "zookeeper"
tags: [zookeeper,bigdata]

---

#### zookeeper集群搭建    


1.在一台机器上解压         

```
tar -zxvf /opt/software/zookeeper-3.4.5-cdh5.3.6.tar.gz -C ./
cd zookeeper-3.4.5-cdh5.3.6
```

2.修改配置文件,并创建数据目录    

```

cp conf/zoo_sample.cfg conf/zoo.cfg

mkdir -p data/zookeeper

vi conf/zoo.cfg

```

```
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
dataDir=/opt/cdh/zookeeper-3.4.5-cdh5.3.6/data/zookeeper

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

server.1=centos1:2888:3888
server.2=centos2:2888:3888
server.3=centos3:2888:3888
```

3.在上面创建的/opt/cdh/zookeeper-3.4.5-cdh5.3.6/data/zookeeper目录下创建myid的文件,并填写内容1   


```
touch myid
[mac@centos1 zookeeper]$ vi myid
[mac@centos1 zookeeper]$ pwd
/opt/cdh/zookeeper-3.4.5-cdh5.3.6/data/zookeeper
```

4.将配置好的zookeeper复制到另外两台机器上，centos2和centos3上   

```
scp -r zookeeper-3.4.5-cdh5.3.6 centos2:/opt/cdh/
scp -r zookeeper-3.4.5-cdh5.3.6 centos3:/opt/cdh/
```

5.进入到centos2中的对应data/zookeeper目录下修改myid内容为2，centos3的myid内容为3    
6.分别进入三台系统中启动zookeeper    

```
./bin/zkServer.sh start
JMX enabled by default
Using config: /opt/cdh/zookeeper-3.4.5-cdh5.3.6/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

```

7.三台都启动成功后才能通过命令查看状态，没启动成功是无法查看状态的    

centos1状态   

```
./bin/zkServer.sh status
JMX enabled by default
Using config: /opt/cdh/zookeeper-3.4.5-cdh5.3.6/bin/../conf/zoo.cfg
Mode: follower

```

centos2状态

```
./bin/zkServer.sh status
JMX enabled by default
Using config: /opt/cdh/zookeeper-3.4.5-cdh5.3.6/bin/../conf/zoo.cfg
Mode: leader
```

centos3状态   

```
./bin/zkServer.sh status
JMX enabled by default
Using config: /opt/cdh/zookeeper-3.4.5-cdh5.3.6/bin/../conf/zoo.cfg
Mode: follower

```

从角色中可以看出centos2目前是领导者状态，该领导者是集群通过选举策略选择后决定的   

没启动完成的时候查看状态如下所示      

```
./bin/zkServer.sh status
JMX enabled by default
Using config: /opt/cdh/zookeeper-3.4.5-cdh5.3.6/bin/../conf/zoo.cfg
Error contacting service. It is probably not running.
```

#### 通过zkdash查看   

通过连接访问服务[http://localhost:8888](http://localhost:8888)

![](../assets/2017/03/2017-03-28_19-41-32.png)

![](../assets/2017/03/2017-03-28_19-45-04.png)

![](../assets/2017/03/2017-03-28_19-46-12.png)
