# reliable环境搭建

1.首先下载docker，[https://www.docker.com/products/overview](https://www.docker.com/products/overview)  
下载mac版本的docker  
备用下载地址[https://mirrors.aliyun.com/docker-toolbox/mac/docker-for-mac/stable/](https://mirrors.aliyun.com/docker-toolbox/mac/docker-for-mac/stable/)

2.下载完成后进行安装，安装完成后并启动

3.下载reliable相关代码

```
git clone https://github.com/reliablejs/reliable-docker-base --depth=1

cd reliable-docker-base

docker build -t="reliable-docker-base" .
```

```
git clone https://github.com/CodeToSurvive1/reliable-master.git
Cloning into 'reliable-master'...
remote: Counting objects: 1484, done.
remote: Total 1484 (delta 0), reused 0 (delta 0), pack-reused 1484
Receiving objects: 100% (1484/1484), 360.56 KiB | 160.00 KiB/s, done.
Resolving deltas: 100% (883/883), done.


cd reliable-master/

docker build -t="reliable-master" .
```

创建目录  
/Users/admin/macaca/reliable/docker/mongodb/data

```
mkdir -p /Users/admin/macaca/reliable/docker/mongodb/data
```

```
git clone https://github.com/reliablejs/reliable-macaca-docker-compose.git --depth=1
Cloning into 'reliable-macaca-docker-compose'...
remote: Counting objects: 8, done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 8 (delta 0), reused 5 (delta 0), pack-reused 0
Unpacking objects: 100% (8/8), done.
TC-D00003330:reliable admin$ 

cd reliable-macaca-docker-compose
```

修改文件docker-compose.yml文件的mongo中的vlumes

```
vi docker-compose.yml
```

修改后如下所示

```
mongo:
  container_name: "reliable_mongo"
  restart: always
  image: mongo
  volumes:
    - /Users/admin/macaca/reliable/docker/mongodb/data:/data/db
    - /etc/localtime:/etc/localtime:ro
```

最后执行启动该命令

```
make start
```

验证docker是否启动成功

```
docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                    NAMES
cb80a3686da9        reliable-master     "/entrypoint.sh /r..."   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp   reliable-master
0f21859f626f        mongo               "/entrypoint.sh mo..."   About a minute ago   Up About a minute   27017/tcp                reliable_mongo
e5d9f2206066        redis               "docker-entrypoint..."   About a minute ago   Up About a minute   6379/tcp                 reliable_redis
```

4.下载slave相关链接程序

```
brew install pkg-config

brew install zeromq

```

```

git clone https://github.com/CodeToSurvive1/reliable-macaca-slave.git

cd reliable-macaca-slave/

make install 

```

如果持续报错，可以修改MakeFile文件中的install部分 

```
install:
	DEVELOPMENT_TEAM_ID=86UCKG2C55 cnpm install 
```
并修改slave部分代码


```


slave:
	./bin/reliable-macaca-slave server -m localhost:8080 --verbose
	
```


最后执行make slave

```

make slave
./bin/reliable-macaca-slave server -m localhost:8080 --verbose

***********************************************
*  version 1.1.1 is outdate                   *
*  run: npm i -g reliable-macaca-slave@2.1.0  *
***********************************************

>> index.js:59:14 [master] pid:4408 worker fork success
>> index.js:62:14 [master] pid:4408 worker online
>> index.js:65:14 [master] pid:4408 listening worker id: 1, pid: 4409, address: 172.31.218.5:4000
>> index.js:65:14 [master] pid:4408 listening worker id: 1, pid: 4409, address: 172.31.218.5:8081
>> index.js:152:12 [worker:1] pid:4409 slave init with 
 { webPort: 8081,
  verbose: true,
  ios: false,
  port: 4000,
  registry: 'http://registry.npmjs.org',
  master: 'http://localhost:8080',
  ip: '172.31.218.5' }
>> index.js:55:12 [worker:1] pid:4409 Zmq Reconnecting...
>> index.js:79:10 [worker:1] pid:4409 Connected to the master server.
>> index.js:9:10 [worker:1] pid:4409 base middlewares attached
>> render.js:15:8 [worker:1] pid:4409 render view path: /Users/admin/macaca/reliable/reliable-macaca-slave/web/views
>> index.js:15:12 [worker:1] pid:4409 middleware: inject registed
>> index.js:15:12 [worker:1] pid:4409 middleware: favicon registed
>> index.js:15:12 [worker:1] pid:4409 middleware: powerby registed
>> index.js:15:12 [worker:1] pid:4409 middleware: static registed
>> channel.js:44:53 [worker:1] pid:4409 accept, endpoint: tcp://172.31.218.5:4000
>> channel.js:73:14 [worker:1] pid:4409 
<--- zmq message 
{ sysInfo: 
   { cpus: 
      [ { model: 'Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz',
          speed: 3200,
          times: { user: 222930, nice: 0, sys: 217620, idle: 6964540, irq: 0 } },
        { model: 'Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz',
          speed: 3200,
          times: { user: 200720, nice: 0, sys: 139090, idle: 7064700, irq: 0 } },
        { model: 'Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz',
          speed: 3200,
          times: { user: 196180, nice: 0, sys: 131500, idle: 7076820, irq: 0 } },
        { model: 'Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz',
          speed: 3200,
          times: { user: 195990, nice: 0, sys: 128380, idle: 7080130, irq: 0 } },
        [length]: 4 ],
     memory: 0.18205928802490234,
     type: 'Darwin',
     platform: 'darwin',
     uptime: '0d 2h 3m',
     release: '15.6.0',
     hostname: 'TC-D00003330.local',
     port: undefined },
  type: 'ack',
  data: 'hello master',
  status: 'ack' }
>> Temp directory not existed, check after one day.
>> index.js:65:14 [master] pid:4408 listening worker id: 1, pid: 4409, address: 172.31.218.5:8081
>> Slave Web Server start with options 
 { webPort: 8081,
  verbose: true,
  ios: false,
  port: 4000,
  registry: 'http://registry.npmjs.org',
  master: 'http://localhost:8080',
  ip: '172.31.218.5' }
>> index.js:65:14 [master] pid:4408 listening worker id: 1, pid: 4409, address: 172.31.218.5:8081


```

证明成功 





