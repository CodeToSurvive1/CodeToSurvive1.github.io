---
layout: "post"
title: "mac系统搭建master-slave环境搭建(非docker版本)"
date: "2016-10-28 10:57"
category: "macaca"

---

#### 配置mongodb数据库

1.首先安装mongodb，教程参考  

[https://codetosurvive1.github.io/posts/mac-mongodb-install.html](https://codetosurvive1.github.io/posts/mac-mongodb-install.html)

2.安装完成后，首先启动mongodb服务端,这里启动如下  

```xml

mongod --dbpath software/mongodb/data/

```

3.首先从github上下载reliable-macaca-docker-compose，然后进入后，解压backup.tar.gz文件，并恢复数据库

```xml

下载reliable-macaca-docker-compose，获取表结构数据  


git clone https://github.com/reliablejs/reliable-macaca-docker-compose.git
Cloning into 'reliable-macaca-docker-compose'...
remote: Counting objects: 37, done.
remote: Total 37 (delta 0), reused 0 (delta 0), pack-reused 37
Unpacking objects: 100% (37/37), done.
Checking connectivity... done.

解压数据结构文件
mac:reliable-macaca-docker-compose mac$ pwd
/Users/mac/software/github/macaca/reliable-macaca-docker-compose
tar -zxvf backup.tar.gz
x backup/
x backup/reliable/
x backup/reliable/projects.bson
x backup/reliable/projects.metadata.json
x backup/reliable/subscribes.bson
x backup/reliable/subscribes.metadata.json
x backup/reliable/system.indexes.bson
x backup/reliable/tasks.bson
x backup/reliable/tasks.metadata.json
x backup/reliable/users.bson
x backup/reliable/users.metadata.json

恢复数据库到本地安装的mongodb数据库中

mongorestore -d reliable backup/reliable
2016-10-28T11:23:15.360+0800	building a list of collections to restore from backup/reliable dir
2016-10-28T11:23:15.361+0800	reading metadata for reliable.projects from backup/reliable/projects.metadata.json
2016-10-28T11:23:15.361+0800	reading metadata for reliable.tasks from backup/reliable/tasks.metadata.json
2016-10-28T11:23:15.362+0800	reading metadata for reliable.users from backup/reliable/users.metadata.json
2016-10-28T11:23:15.362+0800	reading metadata for reliable.subscribes from backup/reliable/subscribes.metadata.json
2016-10-28T11:23:15.414+0800	restoring reliable.projects from backup/reliable/projects.bson
2016-10-28T11:23:15.465+0800	restoring reliable.tasks from backup/reliable/tasks.bson
2016-10-28T11:23:15.513+0800	restoring reliable.subscribes from backup/reliable/subscribes.bson
2016-10-28T11:23:15.513+0800	restoring indexes for collection reliable.subscribes from metadata
2016-10-28T11:23:15.568+0800	restoring reliable.users from backup/reliable/users.bson
2016-10-28T11:23:15.568+0800	restoring indexes for collection reliable.tasks from metadata
2016-10-28T11:23:15.569+0800	restoring indexes for collection reliable.projects from metadata
2016-10-28T11:23:15.644+0800	restoring indexes for collection reliable.users from metadata
2016-10-28T11:23:15.646+0800	finished restoring reliable.tasks (4 documents)
2016-10-28T11:23:15.647+0800	finished restoring reliable.subscribes (0 documents)
2016-10-28T11:23:15.647+0800	finished restoring reliable.projects (1 document)
2016-10-28T11:23:15.710+0800	finished restoring reliable.users (1 document)
2016-10-28T11:23:15.711+0800	done

切换到reliable数据库
> use reliable
switched to db reliable

查看reliable的所有表结构

> show tables
projects
subscribes
tasks
users
>

```

4.验证表结构数据是否成功，新开终端打开mongo客户端   

```xml
打开mongo客户端
mongo

显示数据库结构
show databases;
local     0.000GB
reliable  0.000GB

切换到reliable数据库
use reliable

显示表
show tables;
projects
subscribes
tasks
users

```   

------
#### 配置redis  

1.首先按照[安装教程](https://codetosurvive1.github.io/posts/mac-redis-install.html)进行安装   

2.启动redis   

```xml  

src/redis-server  

```

------

#### 配置reliable-master    

1.首先下载最新版reliable-master    

```git

git clone https://github.com/reliablejs/reliable-master.git
Cloning into 'reliable-master'...
remote: Counting objects: 956, done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 956 (delta 2), reused 0 (delta 0), pack-reused 942
Receiving objects: 100% (956/956), 286.07 KiB | 72.00 KiB/s, done.
Resolving deltas: 100% (512/512), done.
Checking connectivity... done.

```

2.编译项目    

```xml  

make build  


最后出现
Hash: 0570f1997352375ae79c
Version: webpack 1.13.3
Time: 3629ms
       Asset     Size  Chunks             Chunk Names
     auth.js  3.78 kB       0  [emitted]  auth
dashboard.js  1.14 MB       1  [emitted]  dashboard
  history.js  1.14 MB       2  [emitted]  history
     home.js  1.14 MB       3  [emitted]  home
    index.js  2.42 kB       4  [emitted]  index
    login.js  3.34 kB       5  [emitted]  login
 password.js  4.49 kB       6  [emitted]  password
   signup.js  3.99 kB       7  [emitted]  signup
     task.js  1.14 MB       8  [emitted]  task
     user.js   1.4 kB       9  [emitted]  user
    + 191 hidden modules
编译成功


```

3.启动服务    


```xml  
make server

./bin/reliable-master server -p 3333 -w 1 --verbose

*****************************************
*  version 1.0.0 is outdate             *
*  run: npm i -g reliable-master@1.0.8  *
*****************************************

>> index.js:44:14 [master] pid:2244 worker fork success
>> index.js:62:14 [master] pid:2244 worker online
>> index.js:21:10 [worker:1] pid:2246 workder init with config:
 { server: { worker: '1', port: '3333', protocol: 'http', verbose: true },
  database: 'mongodb://localhost/reliable',
  site:
   { title: 'Reliable',
     baseurl: 'http://reliable-test.com',
     locale: 'en-US',
     docurl: 'https://macacajs.github.io/macaca',
     issueurl: 'https://github.com/reliablejs/reliable-master',
     login: true },
  auth:
   { github:
      { client_id: '8bb3d4f7fa7d3d346a58',
        client_secret: '416bdc362cefb378587aa75c1db9bdd4c84a3461' },
     gitlab:
      { protocol: 'http',
        server_url: '127.0.0.1:3000',
        client_id: '8bb3d4f7fa7d3d346a58',
        client_secret: '416bdc362cefb378587aa75c1db9bdd4c84a3461' } },
  mail:
   { name: 'reliable test',
     port: 465,
     host: 'smtp.reliable-test.com',
     secure: true,
     ignoreTLS: true,
     auth: { user: 'test@reliable-test.com', pass: 'reliable' },
     sloganImage: 'https://avatars0.githubusercontent.com/u/9263042?v=3&s=200' },
  plugins: {},
  pluginTextPrefix: 'reliable-plugin',
  pkg:
   { name: 'reliable-master',
     version: '1.0.8',
     description: 'Distributed reliable continuous integration service.',
     keywords: [ 'ci', 'automation', [length]: 2 ],
     bin: { 'reliable-master': './bin/reliable-master' },
     repository:
      { type: 'git',
        url: 'git@github.com:reliablejs/reliable-master.git' },
     dependencies:
      { 'ansi-to-html': '~0.3.0',
        babel: '~5.6.14',
        badgeboard: '~0.1.1',
        bcryptjs: '~2.2.1',
        chalk: '~1.1.1',
        co: '~4.6',
        'co-body': '~1.1.0',
        'co-request': '^0.2.1',
        commander: '~2.0.0',
        'detect-port': '~0.1.3',
        koa: '~0.21.0',
        'koa-csrf': '2.1.3',
        'koa-favicon': '~1.2.0',
        'koa-generic-session': '~1.10.0',
        'koa-redis': '~1.0.1',
        'koa-router': '~3.7.0',
        'koa-static': '~1.4.7',
        mongoose: '=4.1.5',
        nodemailer: '~1.4.0',
        'npm-update': '1.0.2',
        react: '~0.14.0',
        'react-dom': '~0.14.2',
        redis: '~2.3.1',
        'reliable-events': '^0.1.4',
        'reliable-github-oauth': '^1.0.0',
        'reliable-gitlab-oauth': '^1.0.0',
        'reliable-logger': '^1.0.7',
        'reliable-mail': '^0.2.0',
        'reliable-plugin': '^0.1.8',
        validator: '~3.37.0',
        xutil: '~1.0.0',
        yamljs: '~0.2.4',
        zmq: '2.13.0' },
     devDependencies:
      { 'co-mocha': '~1.1.2',
        'command-line-test': '^1.0.5',
        eslint: '~1.1.0',
        'eslint-plugin-react': '~3.2.3',
        istanbul: '*',
        'json-loader': '~0.5.2',
        'jsx-loader': '~0.13.2',
        killing: '~1.0.1',
        mocha: '~2.4.5',
        'pre-commit': '~1.1.1',
        'react-d3-components': '~0.6.0',
        should: '*',
        webpack: '^1.13.1' },
     scripts: { test: 'make test', lint: 'make lint' },
     'pre-commit': [ 'lint', [length]: 1 ],
     engines: { node: '>=4.2.1' },
     homepage: 'https://reliablejs.github.io/reliable',
     license: 'MIT' } }
>> index.js:32:10 [worker:1] pid:2246 base middlewares attached
>> index.js:36:12 [worker:1] pid:2246 middleware: i18n registed
>> render.js:15:8 [worker:1] pid:2246 render view path: /Users/mac/software/github/macaca/reliable-master/web/views
>> index.js:36:12 [worker:1] pid:2246 middleware: inject registed
>> index.js:36:12 [worker:1] pid:2246 middleware: favicon registed
>> index.js:36:12 [worker:1] pid:2246 middleware: powerby registed
>> index.js:36:12 [worker:1] pid:2246 middleware: static registed
>> router.js:41:10 [worker:1] pid:2246 router set
>> index.js:67:14 [master] pid:2244 listening worker id: 1, pid: 2246, address: 172.31.218.249:3333
>> Server start at 2016-10-28 13:17:03 | http://172.31.218.249:3333


```

4.查看页面http://172.31.218.249:3333    

发现是英文版本，断掉服务修改文件    
reliable-master/common/config.j文件中的   
locale: 'en-US',为locale: 'zh-CN',   

5.创建管理员用户   

```xml

mac:reliable-master mac$ make adduser
./bin/reliable-master adduser

*****************************************
*  version 1.0.0 is outdate             *
*  run: npm i -g reliable-master@1.0.8  *
*****************************************


 Do you want create an account? [Y/N]Y

  User Id: root

  Email address: lixiaojiao_hit@163.com

  Password: ********

  isAdmin [Y/N]: Y

  Add user success!

  Goodbye! Have a great day!

```

6.接入slave节点,这里注意，master跟slave都没使用docker，master的端口为3333,对于slave的配置请参考docker篇中的讲解，[连接](https://codetosurvive1.github.io/posts/macaca-master-slave.html)   

```xml

mac:reliable-master mac$ reliable server -m mac.cn:3333 --verbose

```

7.使用管理员登录后，查看页面信息如下   

![成功页面](../assets/2016-10-28_14-00-51.png)
