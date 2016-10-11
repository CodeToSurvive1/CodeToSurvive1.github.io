---
layout: "post"
title: "Mac系统mongodb安装"
date: "2016-10-10 21:58"
category: "other"
tags: [NOSQL]
---

------  

#### mac系统安装步骤  


1.通过homebrew安装mongodb    

```xml
  1.1 brew update |更新Homebrew的包数据库
  1.2 brew install mongodb |安装mongodb数据库
```

2.启动数据库    

```xml
  2.1 首先指定mongodb的数据目录，默认是/data/db，这里改为/Users/mac/software/mongodb/data
  2.2 启动mongodb mongod --dbpath /Users/mac/software/mongodb/data
```

3.启动客户端连接测试成功与否    

```xml
mac:data mac$ mongo
MongoDB shell version: 3.2.10
connecting to: test
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
	http://docs.mongodb.org/
Questions? Try the support group
	http://groups.google.com/group/mongodb-user
Server has startup warnings:
2016-10-11T08:29:52.538+0800 I CONTROL  [initandlisten]
2016-10-11T08:29:52.538+0800 I CONTROL  [initandlisten] ** WARNING: soft rlimits too low. Number of files is 256, should be at least 1000
>
```

```sql
show databases;//查看数据库
show collections;//查看当前数据库的集合，对应的应该是表的概念
use hadoop;//如果没有则创建数据库，有则切换
show dbs;//显示有数据的数据库
db.hadoop.insert({'name':'hadoop'});//向hadoop数据库中插入数据
db.dropDatabase();//删除当前数据库
db.createCollection("users");//在当前连接的数据库中创建集合users
show collections;//显示当前连接的数据库的所有集合
db.users.drop();//删除当前连接数据库的Users集合
```

------  

#### 参考文档


[homebrew安装mongodb官方网站参考连接](https://docs.mongodb.com/master/tutorial/install-mongodb-on-os-x/?_ga=1.189997739.1003684487.1476107793)  

[mongodb shell 使用](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/)
