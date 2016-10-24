---
layout: "post"
title: "macaca持续集成框架CI安装master与slave环境配置"
date: "2016-10-24 14:43"
category: "macaca"

---


#### 安装docker
直接下载安装
[https://download.docker.com/mac/stable/Docker.dmg](https://download.docker.com/mac/stable/Docker.dmg)

------

#### 安装master

1.安装reliable-docker-base基础镜像  

```xml
git clone https://github.com/reliablejs/reliable-docker-base --depth=1
cd reliable-docker-base
docker build -t="reliable-docker-base" .
```

2.安装reliable-master镜像

```xml
git clone https://github.com/reliablejs/reliable-master --depth=1
cd reliable-master
docker build -t="reliable-master" .
```

3.安装reliable-macaca-docker-compose  

```xml

git clone https://github.com/reliablejs/reliable-macaca-docker-compose.git --depth=1
cd reliable-macaca-docker-compose
make start
```

注意：

在make start之前需要修改docker-compose.yml文件，注释掉下面的代码   

```xml

# slave_1:
#   container_name: "slave_1"
#   restart: always
#   image: "reliable-macaca-slave"
#   links:
#     - reliable-master:reliable-master
#   volumes:
#     - /etc/localtime:/etc/localtime:ro
#   command: /reliable-macaca-slave/bin/reliable-macaca-slave server -m reliable-master:8083 --verbose

```


可以安装docker GUI工具来kitematic查看docker中启动的容器,下载地址为[https://github.com/docker/kitematic/releases](https://github.com/docker/kitematic/releases)  

安装后可以看到docker中的容器信息如下

![容器](../assets/2016-10-24_11-09-33.png)  


直接通过浏览器访问页面http://mac.cn:8083可以看到登录页面

![首页](../assets/2016-10-24_09-07-40.png)  

点击sign up直接通过页面注册

![注册页面](../assets/2016-10-24_10-59-48.png)  

进入登录页面    

![登录页面](../assets/2016-10-24_11-03-13.png)

首页页面如下  

![首页页面](../assets/2016-10-24_11-04-41.png)

------

#### 安装slave  

```xml   
brew install pkg-config

brew install zeromq

npm install reliable-macaca-slave -g  

```

安装成功后直接通过命令可以连接master服务端,服务端默认端口为8083   

```xml

reliable server -m mac.cn:8083 --verbose

```

启动成功后台日志信息如下  

```xml

mac:reliable-macaca-docker-compose mac$ reliable server -m mac.cn:8083 --verbose


>> request.js:23:12 [master] pid:18005 get remote update info failed.
>> index.js:57:14 [master] pid:18008 worker fork success
>> index.js:60:14 [master] pid:18008 worker online
>> index.js:63:14 [master] pid:18008 listening worker id: 1, pid: 18009, address: 172.31.218.249:4000
>> index.js:149:12 [worker:1] pid:18009 slave init with
 { webPort: 8080,
  verbose: true,
  port: 4000,
  registry: 'http://registry.npmjs.org',
  master: 'http://mac.cn:8083',
  ip: '172.31.218.249' }
>> index.js:54:12 [worker:1] pid:18009 Zmq Reconnecting...
>> index.js:78:10 [worker:1] pid:18009 Connected to the master server.
>> index.js:9:10 [worker:1] pid:18009 base middlewares attached
>> render.js:15:8 [worker:1] pid:18009 render view path: /usr/local/lib/node_modules/reliable-macaca-slave/web/views
>> index.js:15:12 [worker:1] pid:18009 middleware: inject registed
>> index.js:15:12 [worker:1] pid:18009 middleware: favicon registed
>> index.js:15:12 [worker:1] pid:18009 middleware: powerby registed
>> index.js:15:12 [worker:1] pid:18009 middleware: static registed
>> channel.js:44:53 [worker:1] pid:18009 accept, endpoint: tcp://172.31.218.249:4000
>> Temp directory not existed, check after one day.
>> index.js:63:14 [master] pid:18008 listening worker id: 1, pid: 18009, address: 172.31.218.249:8080
>> Slave Web Server start with options
 { webPort: 8080,
  verbose: true,
  port: 4000,
  registry: 'http://registry.npmjs.org',
  master: 'http://mac.cn:8083',
  ip: '172.31.218.249' }
>> index.js:63:14 [master] pid:18008 listening worker id: 1, pid: 18009, address: 172.31.218.249:8080
>> channel.js:73:14 [worker:1] pid:18009
<--- zmq message
{ sysInfo:
   { cpus:
      [ { model: 'Intel(R) Core(TM) i5-5257U CPU @ 2.70GHz',
          speed: 2700,
          times: { user: 2608850, nice: 0, sys: 2004090, idle: 6215740, irq: 0 } },
        { model: 'Intel(R) Core(TM) i5-5257U CPU @ 2.70GHz',
          speed: 2700,
          times: { user: 1582270, nice: 0, sys: 990180, idle: 8255360, irq: 0 } },
        { model: 'Intel(R) Core(TM) i5-5257U CPU @ 2.70GHz',
          speed: 2700,
          times: { user: 2588680, nice: 0, sys: 1728290, idle: 6510850, irq: 0 } },
        { model: 'Intel(R) Core(TM) i5-5257U CPU @ 2.70GHz',
          speed: 2700,
          times: { user: 1601170, nice: 0, sys: 1009540, idle: 8217100, irq: 0 } },
        [length]: 4 ],
     memory: 0.0027742385864257812,
     type: 'Darwin',
     platform: 'darwin',
     uptime: '0d 3h 11m',
     release: '16.0.0',
     hostname: 'mac.local',
     port: undefined },
  type: 'ack',
  data: 'hello master',
  status: 'ack' }

```

![后台日志1](../assets/2016-10-24_11-14-57.png)

![后台日志2](../assets/2016-10-24_11-15-25.png)

刷新前台页面看到有一个设备已经连接成功

![连接成功](../assets/2016-10-24_11-17-42.png)  

------

自此，macaca的master，slave中CI持续集成框架搭建完毕。  
