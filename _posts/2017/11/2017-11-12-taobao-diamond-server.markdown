---
layout: "post"
title: "diamond-server环境搭建"
date: "2017-11-12 10:37"
category: "j2ee"
tags: [j2ee,javaweb]

---

#### diamond-server环境搭建


1.下载diamond-server   

目前diamond-server的淘宝地址需要通过淘宝svn账号才能登陆，这里是从github上找到的之前版本。  

```

A.   git clone https://github.com/CodeToSurvive1/diamond.git

B.   cd diamond  

C.   git branch -a
        * bbg-dev
        remotes/origin/HEAD -> origin/bbg-dev
        remotes/origin/bbg-dev
        remotes/origin/bbg-dev-github
        remotes/origin/master
D.  可以看到远程分支上总共存在四个分支，本地默认分支是bbg-dev，下面需要切换到master分支，即为淘宝的最初代码

        git checkout -b master origin/master
        意思是切换到远程分支origin/master，并在本地起名为master

E.  可以通过git branch查看是否本地已经切换到master分支
 
```

2.新建数据库并创建表结构  


```
create database config_center;

use config_center;

-- auto-generated definition
CREATE TABLE config_info
(
  id           BIGINT(64) UNSIGNED AUTO_INCREMENT
    PRIMARY KEY,
  data_id      VARCHAR(255) DEFAULT ''                NOT NULL,
  group_id     VARCHAR(128) DEFAULT ''                NOT NULL,
  content      LONGTEXT                               NOT NULL,
  md5          VARCHAR(32) DEFAULT ''                 NOT NULL,
  gmt_create   DATETIME DEFAULT '2010-05-05 00:00:00' NOT NULL,
  gmt_modified DATETIME DEFAULT '2010-05-05 00:00:00' NOT NULL,
  CONSTRAINT uk_config_datagroup
  UNIQUE (data_id, group_id)
);


```

3.修改配置文件打包war包    

修改diamond-server目录下的文件/Users/mac/software/github/diamond/diamond-server/src/main/resources/jdbc.properties  

```
db.url=jdbc:mysql://localhost:3306/config_center?characterEncoding=utf8&connectTimeout=1000&autoReconnect=true
db.user=root
db.password=root
db.initialSize=10
db.maxActive=10
db.maxIdle=5
db.maxWait=5
db.poolPreparedStatements=true
```

修改diamond-server目录下的文件/Users/mac/software/github/diamond/diamond-server/src/main/resources/user.properties  

自定义登录用户名及密码  

```
abc=123
root=root
``` 

修改/Users/mac/software/github/diamond/pom.xml文件中的mysql驱动版本，否则会在后面6处报错  

```
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>6.0.6</version>
    </dependency>
```

编译打war包  

```

mvn clean package -DskipTests 

```

4.下载tomcat并部署diamond-server.war包  


```
下载tomcat并解压
/Users/mac/software/flaginfo/alisoftware/apache-tomcat-8.5.23

部署diamond-server.war包到webapps下
启动tomcat
登录localhost:8080/diamond-server账号root/root即可登录
```

5.百度网盘分享地址   

[https://pan.baidu.com/s/1o8HsqDW](https://pan.baidu.com/s/1o8HsqDW)

6.如果数据库版本太新，可能会在添加配置的时候报错  

```
Request processing failed; nested exception is com.taobao.diamond.server.exception.ConfigServiceException: org.springframework.jdbc.BadSqlGrammarException: PreparedStatementCallback; bad SQL grammar [insert into config_info (data_id,group_id,content,md5,gmt_create,gmt_modified) values(?,?,?,?,?,?)]; nested exception is com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'OPTION SQL_SELECT_LIMIT=DEFAULT' at line 1
```
 