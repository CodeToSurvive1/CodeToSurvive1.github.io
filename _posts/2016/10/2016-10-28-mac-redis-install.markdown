---
layout: "post"
title: "mac安装redis"
date: "2016-10-28 12:35"
category: "other"
tags: [NOSQL]
---



1.安装wget  

```xml

mac:macaca mac$ brew install wget
==> Downloading https://homebrew.bintray.com/bottles/wget-1.18.sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring wget-1.18.sierra.bottle.tar.gz
🍺  /usr/local/Cellar/wget/1.18: 9 files, 1.6M

```

2.下载redis

```xml

wget http://download.redis.io/releases/redis-3.2.5.tar.gz
tar xzf redis-3.2.5.tar.gz
cd redis-3.2.5
make

```

3.启动reddis服务即可  

src/redis-server  

```xml   


 20791:C 28 Oct 12:34:56.784 # Warning: no config file specified, using the default config. In order to specify a config file use src/redis-server /path/to/redis.conf
20791:M 28 Oct 12:34:56.785 * Increased maximum number of open files to 10032 (it was originally set to 2560).
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 3.2.5 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 20791
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

20791:M 28 Oct 12:34:56.786 # Server started, Redis version 3.2.5
20791:M 28 Oct 12:34:56.786 * The server is now ready to accept connections on port 6379


 ```

 4.打开redis客户端测试

 ```xml

 ./src/redis-cli

127.0.0.1:6379> set 'hello' 'wrold'
OK
127.0.0.1:6379> get 'hello'
"wrold"
127.0.0.1:6379>

 ```
