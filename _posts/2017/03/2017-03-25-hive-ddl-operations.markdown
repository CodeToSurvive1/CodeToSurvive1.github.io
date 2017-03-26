---
layout: "post"
title: "hive DDL操作"
date: "2017-03-25 10:52"
category: "hive"
tags: [hive]

---

#### 创建表         

```
create table student(id int ,name string);

create table log(id INT,text string) partitioned by (year string);
```

log表有id和text列，还有一个year的虚拟列  
默认情况下，表被设定为文本内容格式并且是有^A也就是ctrl-a来分割     


#### 查看表    

```
 show tables;
+-----------+--+
| tab_name  |
+-----------+--+
| log       |
| student   |
| xx        |
+-----------+--+

show tables '.*';
+-----------+--+
| tab_name  |
+-----------+--+
| log       |
| student   |
| xx        |
+-----------+--+

show tables '*ent';
+-----------+--+
| tab_name  |
+-----------+--+
| student   |
+-----------+--+

 describe log;
+--------------------------+-----------------------+-----------------------+--+
|         col_name         |       data_type       |        comment        |
+--------------------------+-----------------------+-----------------------+--+
| id                       | int                   |                       |
| text                     | string                |                       |
| year                     | string                |                       |
|                          | NULL                  | NULL                  |
| # Partition Information  | NULL                  | NULL                  |
| # col_name               | data_type             | comment               |
|                          | NULL                  | NULL                  |
| year                     | string                |                       |
+--------------------------+-----------------------+-----------------------+--+
8 rows selected (0.144 seconds)

```


#### 修改和删除表结构   

```
alter table log rename to log_table;

alter table log_table add columns (date string);

alter table log_table add columns(comment string comment 'a comment');

 describe log_table;
+--------------------------+-----------------------+-----------------------+--+
|         col_name         |       data_type       |        comment        |
+--------------------------+-----------------------+-----------------------+--+
| id                       | int                   |                       |
| text                     | string                |                       |
| date                     | string                |                       |
| comment                  | string                | a comment             |
| year                     | string                |                       |
|                          | NULL                  | NULL                  |
| # Partition Information  | NULL                  | NULL                  |
| # col_name               | data_type             | comment               |
|                          | NULL                  | NULL                  |
| year                     | string                |                       |
+--------------------------+-----------------------+-----------------------+--+
10 rows selected (0.153 seconds)


alter table log_table replace columns(comment string comment 'two comment');

 describe log_table;
+--------------------------+-----------------------+-----------------------+--+
|         col_name         |       data_type       |        comment        |
+--------------------------+-----------------------+-----------------------+--+
| comment                  | string                | two comment           |
| year                     | string                |                       |
|                          | NULL                  | NULL                  |
| # Partition Information  | NULL                  | NULL                  |
| # col_name               | data_type             | comment               |
|                          | NULL                  | NULL                  |
| year                     | string                |                       |
+--------------------------+-----------------------+-----------------------+--+
7 rows selected (0.118 seconds)

replace执行之后表的所有列会被替换为新指定的这些列，分区没有被替换 

drop table log_table;
No rows affected (0.717 seconds)


```