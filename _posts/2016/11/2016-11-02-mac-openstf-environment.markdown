---
layout: "post"
title: "mac系统搭建手工测试openstf"
date: "2016-11-02 08:18"
category: "macaca"
tags: [macaca,手工测试,openstf,rethinkdb]
---



#### openstf环境搭建    

官方地址[https://github.com/openstf/stf](https://github.com/openstf/stf)

1.安装依赖环境 	

```xml

brew install rethinkdb graphicsmagick zeromq protobuf yasm pkg-config


```

2.安装stf 		

```xml

npm install -g stf

```


3.启动rethinkdb,注意，启动后会生成数据目录，因此最好放在你想存放数据的地方，这里新建目录


```xml
cd /Users/mac/software/github/macaca

mkdir -p openstf/rethinkdb

cd /Users/mac/software/github/macaca/openstf/rethinkdb

rethinkdb

Recursively removing directory /Users/mac/software/github/macaca/openstf/rethinkdb/rethinkdb_data/tmp
Initializing directory /Users/mac/software/github/macaca/openstf/rethinkdb/rethinkdb_data
Running rethinkdb 2.3.5 (CLANG 8.0.0 (clang-800.0.38))...
Running on Darwin 16.1.0 x86_64
Loading data from directory /Users/mac/software/github/macaca/openstf/rethinkdb/rethinkdb_data
Listening for intracluster connections on port 29015
Listening for client driver connections on port 28015
Listening for administrative HTTP connections on port 8080
Listening on cluster addresses: 127.0.0.1, ::1
Listening on driver addresses: 127.0.0.1, ::1
Listening on http addresses: 127.0.0.1, ::1
To fully expose RethinkDB on the network, bind to all addresses by running rethinkdb with the `--bind all` command line option.
Server ready, "mac_local_x2o" 01cdb2a8-0138-4245-9c28-02b3d95a9a6c



``` 	

4.启动stf 	

新建窗口，启动stf 		

stf local 		

```xml

stf local
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js migrate"
INF/db 3337 [*] Connecting to 127.0.0.1:28015
INF/db:setup 3337 [*] Database "stf" created
INF/db:setup 3337 [*] Table "users" created
INF/db:setup 3337 [*] Table "vncauth" created
INF/db:setup 3337 [*] Table "devices" created
INF/db:setup 3337 [*] Table "logs" created
INF/db:setup 3337 [*] Index "users"."adbKeys" created
INF/db:setup 3337 [*] Waiting for index "users"."adbKeys"
INF/db:setup 3337 [*] Index "vncauth"."response" created
INF/db:setup 3337 [*] Waiting for index "vncauth"."response"
INF/db:setup 3337 [*] Index "devices"."owner" created
INF/db:setup 3337 [*] Waiting for index "devices"."owner"
INF/db:setup 3337 [*] Index "vncauth"."responsePerDevice" created
INF/db:setup 3337 [*] Waiting for index "vncauth"."responsePerDevice"
INF/db:setup 3337 [*] Index "devices"."present" created
INF/db:setup 3337 [*] Waiting for index "devices"."present"
INF/db:setup 3337 [*] Table "accessTokens" created
INF/db:setup 3337 [*] Index "devices"."providerChannel" created
INF/db:setup 3337 [*] Waiting for index "devices"."providerChannel"
INF/db:setup 3337 [*] Index "accessTokens"."email" created
INF/db:setup 3337 [*] Waiting for index "accessTokens"."email"
INF/db:setup 3337 [*] Index "devices"."owner" is ready
INF/db:setup 3337 [*] Index "accessTokens"."email" is ready
INF/db:setup 3337 [*] Index "users"."adbKeys" is ready
INF/db:setup 3337 [*] Index "vncauth"."response" is ready
INF/db:setup 3337 [*] Index "vncauth"."responsePerDevice" is ready
INF/db:setup 3337 [*] Index "devices"."providerChannel" is ready
INF/db:setup 3337 [*] Index "devices"."present" is ready
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js triproxy app001 --bind-pub tcp://127.0.0.1:7111 --bind-dealer tcp://127.0.0.1:7112 --bind-pull tcp://127.0.0.1:7113"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js triproxy dev001 --bind-pub tcp://127.0.0.1:7114 --bind-dealer tcp://127.0.0.1:7115 --bind-pull tcp://127.0.0.1:7116"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js processor proc001 --connect-app-dealer tcp://127.0.0.1:7112 --connect-dev-dealer tcp://127.0.0.1:7115"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js processor proc002 --connect-app-dealer tcp://127.0.0.1:7112 --connect-dev-dealer tcp://127.0.0.1:7115"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js reaper reaper001 --connect-push tcp://127.0.0.1:7116 --connect-sub tcp://127.0.0.1:7111"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js provider --name mac.local --min-port 7400 --max-port 7700 --connect-sub tcp://127.0.0.1:7114 --connect-push tcp://127.0.0.1:7116 --group-timeout 900 --public-ip localhost --storage-url http://localhost:7100/ --adb-host 127.0.0.1 --adb-port 5037 --vnc-initial-size 600x800"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js auth-mock --port 7120 --secret kute kittykat --app-url http://localhost:7100/"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js app --port 7105 --secret kute kittykat --auth-url http://localhost:7100/auth/mock/ --websocket-url http://localhost:7110/"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js api --port 7106 --secret kute kittykat --connect-push tcp://127.0.0.1:7113 --connect-sub tcp://127.0.0.1:7111"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js websocket --port 7110 --secret kute kittykat --storage-url http://localhost:7100/ --connect-sub tcp://127.0.0.1:7111 --connect-push tcp://127.0.0.1:7113"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js storage-temp --port 7102"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js storage-plugin-image --port 7103 --storage-url http://localhost:7100/"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js storage-plugin-apk --port 7104 --storage-url http://localhost:7100/"
INF/util:procutil 3336 [*] Forking "/usr/local/lib/node_modules/stf/lib/cli.js poorxy --port 7100 --app-url http://localhost:7105/ --auth-url http://localhost:7120/ --api-url http://localhost:7106/ --websocket-url http://localhost:7110/ --storage-url http://localhost:7102/ --storage-plugin-image-url http://localhost:7103/ --storage-plugin-apk-url http://localhost:7104/"
INF/triproxy 3345 [app001] PUB socket bound on tcp://127.0.0.1:7111
INF/triproxy 3345 [app001] DEALER socket bound on tcp://127.0.0.1:7112
INF/triproxy 3345 [app001] PULL socket bound on tcp://127.0.0.1:7113
INF/triproxy 3346 [dev001] PUB socket bound on tcp://127.0.0.1:7114
INF/triproxy 3346 [dev001] DEALER socket bound on tcp://127.0.0.1:7115
INF/triproxy 3346 [dev001] PULL socket bound on tcp://127.0.0.1:7116
INF/reaper 3349 [reaper001] Subscribing to permanent channel "*ALL"
INF/reaper 3349 [reaper001] Reaping devices with no heartbeat
INF/db 3347 [proc001] Connecting to 127.0.0.1:28015
INF/db 3348 [proc002] Connecting to 127.0.0.1:28015
INF/db 3349 [reaper001] Connecting to 127.0.0.1:28015
INF/processor 3347 [proc001] App dealer connected to "tcp://127.0.0.1:7112"
INF/processor 3347 [proc001] Device dealer connected to "tcp://127.0.0.1:7115"
INF/processor 3348 [proc002] App dealer connected to "tcp://127.0.0.1:7112"
INF/processor 3348 [proc002] Device dealer connected to "tcp://127.0.0.1:7115"
INF/reaper 3349 [reaper001] Receiving input from "tcp://127.0.0.1:7111"
INF/reaper 3349 [reaper001] Sending output to "tcp://127.0.0.1:7116"
INF/poorxy 3358 [*] Listening on port 7100
INF/storage:plugins:image 3356 [*] Listening on port 7103
INF/auth-mock 3351 [*] Listening on port 7120
INF/provider 3350 [*] Subscribing to permanent channel "tNTchuTuQWeSBGur9r8xsg=="
INF/storage:plugins:apk 3357 [*] Listening on port 7104
INF/provider 3350 [*] Sending output to "tcp://127.0.0.1:7116"
INF/provider 3350 [*] Receiving input from "tcp://127.0.0.1:7114"
INF/storage:temp 3355 [*] Listening on port 7102
INF/app 3352 [*] Using pre-built resources
INF/app 3352 [*] Listening on port 7105
INF/db 3352 [*] Connecting to 127.0.0.1:28015
INF/websocket 3354 [*] Subscribing to permanent channel "*ALL"
INF/websocket 3354 [*] Listening on port 7110
INF/db 3354 [*] Connecting to 127.0.0.1:28015
INF/websocket 3354 [*] Sending output to "tcp://127.0.0.1:7113"
INF/websocket 3354 [*] Receiving input from "tcp://127.0.0.1:7111"
INF/api 3353 [*] Subscribing to permanent channel "*ALL"
INF/api 3353 [*] Listening on port 7106
INF/api 3353 [*] Sending output to "tcp://127.0.0.1:7113"
INF/api 3353 [*] Receiving input from "tcp://127.0.0.1:7111"
INF/db 3353 [*] Connecting to 127.0.0.1:28015
INF/provider 3350 [*] Tracking devices

```

5.访问地址，http://localhost:7105/   	

![首页](../assets/2016/11/2016-11-02_08-35-19.png)	

随便输入用户名及邮箱 	

![用户名及邮箱](../assets/2016/11/2016-11-02_08-38-41.png)	

![设备首页](../assets/2016/11/2016-11-02_08-39-39.png)

这时候随便插入一台android设备后，会出现页面 	

![pending](../assets/2016/11/2016-11-02_08-40-32.png)

一会后识别成功	

![mi5](../assets/2016/11/2016-11-02_08-40-51.png) 	

点击mi5图标进入手机操控页面 	

![手机操控页面](../assets/2016/11/2016-11-02_08-43-07.png)





