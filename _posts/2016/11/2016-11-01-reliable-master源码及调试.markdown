---
layout: "post"
title: "reliable-master源码安装及调试及插件配置"
date: "2016-11-01 08:52"
category: "macaca"
tags: [macaca,reliablejs,reliable-master]
---


#### 源码环境下载搭建 		


1.从github上下载最新的源码 		

```xml

git clone https://github.com/reliablejs/reliable-master.git
Cloning into 'reliable-master'...
remote: Counting objects: 992, done.
remote: Compressing objects: 100% (49/49), done.
remote: Total 992 (delta 7), reused 0 (delta 0), pack-reused 942
Receiving objects: 100% (992/992), 301.72 KiB | 66.00 KiB/s, done.
Resolving deltas: 100% (517/517), done.
Checking connectivity... done.

```	

2.编译源码  		

```xml

cd reliable-master/
make build 

```

3.启动服务  

```xml

make server
./bin/reliable-master server -p 3333 -w 1 --verbose
>> index.js:44:14 [master] pid:3521 worker fork success
>> index.js:62:14 [master] pid:3521 worker online
>> index.js:21:10 [worker:1] pid:3522 workder init with config:
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
     version: '1.1.0',
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
>> index.js:32:10 [worker:1] pid:3522 base middlewares attached
>> index.js:36:12 [worker:1] pid:3522 middleware: i18n registed
>> render.js:15:8 [worker:1] pid:3522 render view path: /Users/mac/software/github/macaca/reliable-master/web/views
>> index.js:36:12 [worker:1] pid:3522 middleware: inject registed
>> index.js:36:12 [worker:1] pid:3522 middleware: favicon registed
>> index.js:36:12 [worker:1] pid:3522 middleware: powerby registed
>> index.js:36:12 [worker:1] pid:3522 middleware: static registed
>> router.js:41:10 [worker:1] pid:3522 router set
>> index.js:67:14 [master] pid:3521 listening worker id: 1, pid: 3522, address: 172.31.218.249:3333
>> Server start at 2016-11-01 08:57:40 | http://172.31.218.249:3333

```

前提是需要首先启动mongodb服务及redis服务，请参考连接[mac系统搭建master-slave环境搭建(非docker版本)](https://codetosurvive1.github.io/posts/mcaca-master-slave-no-docker.html)		

------

#### 邮箱配置 

默认情况下，reliable-master是配置了邮箱服务插件的，那么该如何使用邮箱服务呢	

这里注册了一个163邮箱,下面进行单独的说明 

1.申请个人邮箱****#163.com，密码qwer1234		

2.修改/Users/mac/software/github/macaca/reliable-master/common目录下的config.js文件 		

默认值为		

```xml
mail: {
    name: 'reliable test',
    port: 465,
    host: 'smtp.reliable-test.com',
    secure: true,
    ignoreTLS: true,
    auth: {
      user: 'test@reliable-test.com',
      pass: 'reliable'
    },
    sloganImage: 'https://avatars0.githubusercontent.com/u/9263042?v=3&s=200'
  },

```

修改为注册的邮箱		

```xml

mail: {
    name: '李小蛟',
    port: 465,
    host: 'smtp.163.com',
    secure: true,
    ignoreTLS: true,
    auth: {
      user: '***@163.com',
      pass: 'qwer1234'
    },
    sloganImage: 'https://avatars0.githubusercontent.com/u/9263042?v=3&s=200'
  },

```

并修改语言版本locale: 'en-US',为locale: 'zh-CN',


3.重新编译reliable-master 		

```xml

mac:reliable-master mac$ make build
Hash: 6a4c6d908ed02be70570
Version: webpack 1.13.3
Time: 4289ms
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


```

4.启动master服务(前提启动mongodb及redis服务) 		

```xml

make server

./bin/reliable-master server -p 3333 -w 1 --verbose

*****************************************
*  version 1.0.0 is outdate             *
*  run: npm i -g reliable-master@1.1.0  *
*****************************************

>> index.js:44:14 [master] pid:4401 worker fork success
>> index.js:62:14 [master] pid:4401 worker online
>> index.js:21:10 [worker:1] pid:4404 workder init with config:
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
   { name: '李小蛟',
     port: 465,
     host: 'smtp.163.com',
     secure: true,
     ignoreTLS: true,
     auth: { user: 'bankcomm_test@163.com', pass: 'qwer1234' },
     sloganImage: 'https://avatars0.githubusercontent.com/u/9263042?v=3&s=200' },
  plugins: {},
  pluginTextPrefix: 'reliable-plugin',
  pkg: 
   { name: 'reliable-master',
     version: '1.1.0',
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
>> index.js:32:10 [worker:1] pid:4404 base middlewares attached
>> index.js:36:12 [worker:1] pid:4404 middleware: i18n registed
>> render.js:15:8 [worker:1] pid:4404 render view path: /Users/mac/software/github/macaca/reliable-master/web/views
>> index.js:36:12 [worker:1] pid:4404 middleware: inject registed
>> index.js:36:12 [worker:1] pid:4404 middleware: favicon registed
>> index.js:36:12 [worker:1] pid:4404 middleware: powerby registed
>> index.js:36:12 [worker:1] pid:4404 middleware: static registed
>> router.js:41:10 [worker:1] pid:4404 router set
>> index.js:67:14 [master] pid:4401 listening worker id: 1, pid: 4404, address: 172.31.218.249:3333
>> Server start at 2016-11-01 09:11:20 | http://172.31.218.249:3333

```

5.访问http://172.31.218.249:3333地址，	

![邮件](../assets/2016-11-01_09-33-32.png)


![发送邮件](../assets/2016-11-01_09-44-45.png)

登录邮箱查看是否收到发送的邮件。		

在发送邮件之前，请确定自己的163邮箱已经开启stmp及pop服务，才可以发送邮件。 	

![设置stmp服务](../assets/2016-11-01_10-03-09.png) 		

需要注意，我这里使用新注册的邮箱，开启stmp服务是需要设置授权码的，但是reliable-master中的配置文件是不支持这种授权码形式的，所以新注册的163邮箱是无法使用的，但是以前注册的163邮箱是可以的，我这里使用的是以前的163邮箱。qq邮箱现在也是需要设置授权码才能开通stmp服务的。如果163邮箱和qq邮箱都无法使用，那还是找一家邮箱注册用户不太多的来测试邮件插件吧，小公司用户量少的应该基本不会有授权码这一单独设置的。		

6.可以订阅任务跑脚本尝试一下了.


#### 插件安装   

对于插件的安装还是很简单的，只需要在config文件中添加插件名称设置为true即可，目前官方提供的插件有两个，dingdingtalk跟slack      


1.配置    

下面是配置方式     



```xml

  plugins: {
      'reliable-plugin-dingtalk': true,
      'reliable-plugin-slack':true
  },  

```

2.安装插件      

然后进入reliable-master目录下安装reliable-plugin-dingtalk及reliable-plugin-slack插件        

```xml

npm install --save reliable-plugin-slack 
reliable-plugin-slack@0.1.3 node_modules/reliable-plugin-slack
├── co-body@4.2.0 (inflation@2.0.0, qs@4.0.0, raw-body@2.1.7, type-is@1.6.13)
├── koa-static@2.0.0 (debug@2.2.0, koa-send@3.1.1)
├── request@2.76.0 (aws-sign2@0.6.0, tunnel-agent@0.4.3, forever-agent@0.6.1, oauth-sign@0.8.2, caseless@0.11.0, is-typedarray@1.0.0, stringstream@0.0.5, isstream@0.1.2, aws4@1.5.0, json-stringify-safe@5.0.1, extend@3.0.0, node-uuid@1.4.7, qs@6.3.0, combined-stream@1.0.5, mime-types@2.1.12, tough-cookie@2.3.2, form-data@2.1.1, hawk@3.1.3, http-signature@1.1.1, har-validator@2.0.6)
└── react@15.3.2 (object-assign@4.1.0, loose-envify@1.3.0, fbjs@0.8.5)


npm install --save reliable-plugin-dingtalk
reliable-plugin-dingtalk@0.1.4 node_modules/reliable-plugin-dingtalk
├── only@0.0.2
├── koa-static@2.0.0 (debug@2.2.0, koa-send@3.1.1)
├── co-body@4.2.0 (inflation@2.0.0, qs@4.0.0, type-is@1.6.13, raw-body@2.1.7)
├── request@2.76.0 (aws-sign2@0.6.0, tunnel-agent@0.4.3, oauth-sign@0.8.2, forever-agent@0.6.1, is-typedarray@1.0.0, caseless@0.11.0, stringstream@0.0.5, aws4@1.5.0, isstream@0.1.2, json-stringify-safe@5.0.1, extend@3.0.0, node-uuid@1.4.7, qs@6.3.0, combined-stream@1.0.5, mime-types@2.1.12, tough-cookie@2.3.2, form-data@2.1.1, hawk@3.1.3, http-signature@1.1.1, har-validator@2.0.6)
├── react@15.3.2 (object-assign@4.1.0, loose-envify@1.3.0, fbjs@0.8.5)
└── babel-preset-react@6.16.0 (babel-plugin-syntax-jsx@6.18.0, babel-plugin-syntax-flow@6.18.0, babel-plugin-transform-react-jsx@6.8.0, babel-plugin-transform-flow-strip-types@6.18.0, babel-plugin-transform-react-display-name@6.8.0, babel-plugin-transform-react-jsx-source@6.9.0, babel-plugin-transform-react-jsx-self@6.11.0)



```

3.编译    

```xml

make build
Hash: 4430e4f8d53acc4b7889
Version: webpack 1.13.3
Time: 3733ms
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


```

4.运行    

```

make server
./bin/reliable-master server -p 3333 -w 1 --verbose
>> index.js:44:14 [master] pid:15102 worker fork success
>> index.js:62:14 [master] pid:15102 worker online
>> index.js:21:10 [worker:1] pid:15103 workder init with config:
 { server: { worker: '1', port: '3333', protocol: 'http', verbose: true },
  database: 'mongodb://localhost/reliable',
  site: 
   { title: 'Reliable',
     baseurl: 'http://reliable-test.com',
     locale: 'zh-CN',
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
   { name: '李小蛟',
     port: 465,
     host: 'smtp.163.com',
     secure: true,
     ignoreTLS: true,
     auth: { user: 'lixiaojiao_hit@163.com', pass: 'loveTing1314' },
     sloganImage: 'https://avatars0.githubusercontent.com/u/9263042?v=3&s=200' },
  plugins: 
   { 'reliable-plugin-dingtalk': true,
     'reliable-plugin-slack': true },
  pluginTextPrefix: 'reliable-plugin',
  pkg: 
   { name: 'reliable-master',
     version: '1.1.0',
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
        'reliable-plugin-dingtalk': '^0.1.4',
        'reliable-plugin-slack': '^0.1.3',
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
>> index.js:32:10 [worker:1] pid:15103 base middlewares attached
>> index.js:36:12 [worker:1] pid:15103 middleware: i18n registed
>> render.js:15:8 [worker:1] pid:15103 render view path: /Users/mac/software/github/macaca/reliable-master/web/views
>> index.js:36:12 [worker:1] pid:15103 middleware: inject registed
>> index.js:36:12 [worker:1] pid:15103 middleware: favicon registed
>> index.js:36:12 [worker:1] pid:15103 middleware: powerby registed
>> index.js:36:12 [worker:1] pid:15103 middleware: static registed
plugin: reliable-plugin-dingtalk registered
plugin: reliable-plugin-slack registered
>> router.js:41:10 [worker:1] pid:15103 router set
>> index.js:67:14 [master] pid:15102 listening worker id: 1, pid: 15103, address: 172.31.218.249:3333
>> Server start at 2016-11-01 16:40:13 | http://172.31.218.249:3333

```

5.查看页面  

![插件截图](../assets/2016-11-01_16-42-04.png)

![叮叮](../assets/2016-11-01_16-42-55.png)

![slack](../assets/2016-11-01_16-43-40.png)


------

#### 调试开发reliable-master       

这里使用vscode进行调试  

由于reliable-master使用cluster模式，在启动服务的时候会启动多进程，这样导致无法调试，因此这里将服务启动后的核心服务端程序单独摘出来，编写js来调试 

1.编写js脚本    

在/Users/mac/software/github/macaca/reliable-master/init/目录下新建runner.js，并编写下面内容  


```xml

'use strict';

const cluster = require('cluster');

const Web = require('../web');
const Task = require('../core').Task;
const _ = require('../common/utils/helper');

const defaultOpts = require('../common/config').get();
const program = require('commander');

let options = _.clone(defaultOpts);
_.merge(options.server, _.getConfig(program));
options.pkg = require('../package');

/**
 * Created by mac on 2016/10/31.
 */
Web.init(options, () => {
    Task.bind();
});


```

2.打开debug标签     

![debug](../assets/2016-11-01_16-55-15.png)

![json](../assets/2016-11-01_16-57-01.png)

修改路径为刚才的runner.js 

![修改后的json](../assets/2016-11-01_16-59-48.png)

3.在runner.js中打断点，继续重新点击debug按钮  

![debug_point](../assets/2016-11-01_16-58-20.png)

在/Users/mac/software/github/macaca/reliable-master/web/controllers/dashboard.js中打入断点    

刷新http://172.31.218.249:8080/dashboard页面看到截图信息  

![项目列表](../assets/2016-11-01_17-10-56.png)  


