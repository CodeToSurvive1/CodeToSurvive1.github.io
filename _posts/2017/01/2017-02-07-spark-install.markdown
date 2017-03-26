---
layout: "post"
title: "spark"
date: "2017-02-07 08:52"
category: "spark"
tags: [spark,hadoop,bigdata]

---


#### spark下载安装  

1.下载    

进入spark官方页面http://spark.apache.org/downloads.html

选择Spark版本为1.6.1     
打包类型为Pre-built for hadoop 2.6版本         
然后选择下载      


2.安装
下载完成后直接解压，进入目录，并执行命令即可：     

```bash

./bin/spark-shell 

 sc.version
res3: String = 1.6.1      

scala> sc.textFile("README.md")
res4: org.apache.spark.rdd.RDD[String] = README.md MapPartitionsRDD[1] at textFile at <console>:28

scala> res4.count()
res5: Long = 95                                                                 

scala> res4.first()
res6: String = # Apache Spark

查看页面可以看到相关信息 
http://localhost:4040/jobs/
```



