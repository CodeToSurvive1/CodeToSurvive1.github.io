---
layout: "post"
title: "hive安装，内嵌模式及本地模式"
date: "2016-10-29 13:04"
category: "hive"
tags: [大数据,hive]
---


#### hive内嵌安装步骤		

内嵌模式，特点是：hive服务和metastore服务运行在同一个进程中，derby服务也运行在该进程中。
该模式无需特殊配置		


1.首先去cdh官网下载[hive](http://archive.cloudera.com/cdh5/cdh/5/hive-0.13.1-cdh5.3.6.tar.gz)    


2.解压缩,并切换到hive配置文件目录中      

```xml

tar -zxvf hive-0.13.1-cdh5.3.6.tar.gz -C newcdh/

cp hive-log4j.properties.template hive-log4j.properties
cp hive-env.sh.template hive-env.sh

```

3.修改hive-env.sh文件，设置下面的两行配置，HADOOP_HOME和HIVE_CONF_DIR配置   

```properties

HADOOP_HOME=/Users/mac/software/cdh/hadoop-2.5.0-cdh5.3.6/
export HIVE_CONF_DIR=/Users/mac/software/newcdh/hive-0.13.1-cdh5.3.6/conf

```

4.这时候可以直接执行启动hive命令了    

```xml

mac:hive-0.13.1-cdh5.3.6 mac$ ./bin/hive

Logging initialized using configuration in file:/Users/mac/software/newcdh/hive-0.13.1-cdh5.3.6/conf/hive-log4j.properties
hive> 


```

5.启动成功后，执行命令验证是否安装成功。	


```xml

hive> show databases;
OK
default
Time taken: 0.539 seconds, Fetched: 1 row(s)
hive> use default;
OK
Time taken: 0.021 seconds
hive> show tables;
OK
Time taken: 0.037 seconds

```

6.同时查看hive目录下生成了metastore_db目录结构,这是由于hive默认使用derby数据库，因此在启动过程中使用derby数据库存储表结构相关信息,注意，这里的derby数据库是单会话的，一次只能有一个连接，并且derby数据库会在当前目录下生成		

------


#### hive本地安装配置及高级配置			

本地模式，特点是：hive服务和metastore服务运行在同一个进程中，mysql是单独的进程，可以在同一台机器上，也可以在远程机器上。		

7.下面配置修改为mysql数据库，这样可以支持多会话连接,在conf目录下新建hive-site.xml文件并添加如下内容    


```xml

cd /Users/mac/software/newcdh/hive-0.13.1-cdh5.3.6/conf

touch hive-hive-site.xml

vi hive-hive-site.xml


<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>

	<property>
	  <name>javax.jdo.option.ConnectionURL</name>
	  <value>jdbc:mysql://localhost:3306/hive_metastore?createDatabaseIfNotExist=true</value>
	  <description>mysql连接地址</description>
	</property>

	<property>
	  <name>javax.jdo.option.ConnectionDriverName</name>
	  <value>com.mysql.jdbc.Driver</value>
	  <description>mysql驱动类</description>
	</property>

	<property>
	  <name>javax.jdo.option.ConnectionUserName</name>
	  <value>hive</value>
	  <description>刚刚创建的用户名hive</description>
	</property>

	<property>
	  <name>javax.jdo.option.ConnectionPassword</name>
	  <value>123456</value>
	  <description>hive用户对应的mysql密码</description>
	</property>

</configuration>


```

同时需要讲mysql驱动程序拷贝到lib目录下，否则会报驱动程序找不到的错误	
cp ../../cdh/hive-0.13.1-cdh5.3.6/lib/mysql-connector-java-5.1.27-bin.jar ./lib/
重启hive，验证	

```xml

mac:hive-0.13.1-cdh5.3.6 mac$ ./bin/hive

Logging initialized using configuration in file:/Users/mac/software/newcdh/hive-0.13.1-cdh5.3.6/conf/hive-log4j.properties
hive> show databases;
OK
default
Time taken: 0.484 seconds, Fetched: 1 row(s)
hive> use default;
OK
Time taken: 0.02 seconds
hive> show tables;
OK
sample_07
sample_08
xxx

```

这里便是因为hive使用了我们配置的mysql数据库，mysql数据库中原来配置过一次hive表，里面有之前建立的表,由此验证配置mysql成功		
这时查询表数据会报错，因为没有启动hdfs服务	

```xml

select * from xxx;
FAILED: SemanticException Unable to determine if hdfs://mac.cn:8020/user/hive/warehouse/xxx is encrypted: java.net.ConnectException: Call From mac.local/172.31.218.249 to mac.cn:8020 failed on connection exception: java.net.ConnectException: Connection refused; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused

```

启动hdfs服务		

```xml

cd software/cdh/hadoop-2.5.0-cdh5.3.6/

./sbin/start-dfs.sh 

```

再次查询后		

```xml

hive> select * from xxx;
OK

Time taken: 0.439 seconds

```


8.配置在查询结果中显示列名称 

在hive-site.xml中配置 		

```xml

<property>
	<name>hive.cli.print.header</name>
	<value>true</value>
	<description>Whether to print the names of the columns in query output.</description>
</property>

```

重新进入hive命令行验证是否成功	

```xml

hive> select * from xxx;
OK
xxx.id
Time taken: 0.524 seconds

```


9.配置默认显示当前所在的数据库 	

在hive-site.xml中添加 	

```xml

<property>
	<name>hive.cli.print.current.db</name>
	<value>true</value>
	<description>Whether to include the current database in the Hive prompt.</description>
</property>

```

重新进入hive命令行验证是否成功	

```xml

mac:hive-0.13.1-cdh5.3.6 mac$ ./bin/hive

Logging initialized using configuration in file:/Users/mac/software/newcdh/hive-0.13.1-cdh5.3.6/conf/hive-log4j.properties
hive (default)> show databases;
OK
database_name
default
Time taken: 0.484 seconds, Fetched: 1 row(s)
hive (default)> 

```




