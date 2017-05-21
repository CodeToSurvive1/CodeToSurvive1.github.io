---
layout: "post"
title: "flume安装"
date: "2017-03-28 19:17"
category: "flume"
tags: [flume,bigdata]

---

#### flume安装    

1.解压flume  

```
tar -zxvf ../software/flume-ng-1.5.0-cdh5.3.6.tar.gz -C .

mv apache-flume-1.5.0-cdh5.3.6-bin/ flume-1.5.0-cdh5.3.6-bin/
```

2.修改配置文件    

```
cd conf/
cp flume-env.sh.template flume-env.sh

vi flume-env.sh

添加java_home环境变量
export JAVA_HOME=/opt/modules/jdk1.7.0_67

```

3.创建日志目录  

```
mkdir logs
```

#### flume使用
