---
layout: post
category: "macaca"
title:  "app-inspector代码启动"
tags: [macaca,nodejs]
---

# app-inspector代码启动

## 1.从github下载代码

```xml
mac:macacajs mac$ git clone https://github.com/macacajs/app-inspector.git
Cloning into \'app-inspector\'...
remote: Counting objects: 352, done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 352 (delta 2), reused 0 (delta 0), pack-reused 338
Receiving objects: 100% (352/352), 186.55 KiB | 55.00 KiB/s, done.
Resolving deltas: 100% (160/160), done.
Checking connectivity... done.
```

## 2.安装依赖包，安装成功

```xml
mac:macacajs mac$ cd app-inspector/
mac:app-inspector mac$ ls
HISTORY.md      README.md       index.js        package.json        views
LICENSE         assets          issue_template.md   public          webpack.config.js
Makefile        bin         lib         test
mac:app-inspector mac$ npm install
npm WARN deprecated cross-spawn-async@2.2.4: cross-spawn no longer requires a build toolchain, use it instead!
npm WARN deprecated win-spawn@2.0.0: use [cross-spawn](https://github.com/IndigoUnited/node-cross-spawn) or [cross-spawn-async](https://github.com/IndigoUnited/node-cross-spawn-async) instead.
npm WARN deprecated tough-cookie@2.2.2: ReDoS vulnerability parsing Set-Cookie https://nodesecurity.io/advisories/130
。。。。
nunjucks@2.5.2 node_modules/nunjucks
├── asap@2.0.5
├── yargs@3.32.0 (decamelize@1.2.0, y18n@3.2.1, camelcase@2.1.1, window-size@0.1.4, os-locale@1.4.0, string-width@1.0.2, cliui@3.2.0)
└── chokidar@1.6.0 (path-is-absolute@1.0.0, async-each@1.0.1, inherits@2.0.3, glob-parent@2.0.0, is-glob@2.0.1, is-binary-path@1.0.1, readdirp@2.1.0, anymatch@1.3.0, fsevents@1.0.14)
webpack@1.13.2 node_modules/webpack
├── interpret@0.6.6
├── tapable@0.1.10
├── async@1.5.2
├── clone@1.0.2
├── supports-color@3.1.2 (has-flag@1.0.0)
├── loader-utils@0.2.16 (object-assign@4.1.0, big.js@3.1.3, emojis-list@2.0.1, json5@0.5.0)
├── enhanced-resolve@0.9.1 (graceful-fs@4.1.8, memory-fs@0.2.0)
├── mkdirp@0.5.1 (minimist@0.0.8)
├── optimist@0.6.1 (wordwrap@0.0.3, minimist@0.0.10)
├── acorn@3.3.0
├── memory-fs@0.3.0 (errno@0.1.4, readable-stream@2.1.5)
├── webpack-core@0.6.8 (source-map@0.4.4, source-list-map@0.1.6)
├── uglify-js@2.6.4 (async@0.2.10, uglify-to-browserify@1.0.2, source-map@0.5.6, yargs@3.10.0)
├── node-libs-browser@0.6.0 (https-browserify@0.0.0, punycode@1.4.1, string_decoder@0.10.31, tty-browserify@0.0.0, path-browserify@0.0.0, constants-browserify@0.0.1, os-browserify@0.1.2, process@0.11.9, assert@1.4.1, domain-browser@1.1.7, querystring-es3@0.2.1, timers-browserify@1.4.2, stream-browserify@1.0.0, events@1.1.1, readable-stream@1.1.14, util@0.10.3, console-browserify@1.1.0, url@0.10.3, vm-browserify@0.0.4, http-browserify@1.7.0, buffer@4.9.1, browserify-zlib@0.1.4, crypto-browserify@3.2.8)
└── watchpack@0.2.9 (graceful-fs@4.1.8, async@0.9.2, chokidar@1.6.0)
```

## 3.修改package.json 中的uuid，并使用npm run start

```xml
需要先修改 package.json 里面的 UID：
"start": "APP_INSPECTOR=dev ./bin/app-inspector -u DU2MYN146D002912 --verbose",
使用 npm run start 启动
```

## 4.或者使用下面的

如果有 APP_INSPECTOR=dev环境变量，服务端会使用 webpack-dev-middle，静态资源直接从源码动态编译。

```xml
mac:app-inspector mac$ APP_INSPECTOR=dev ./bin/app-inspector -u DU2MYN146D002912
```

或者

如果没有 APP_INSPECTOR=dev环境变量，服务器会从 dist 目录读取打包后的静态文件，而打包文件是没有提交到 git 的。
如果要得到 dist 文件需要使用npm run build编译。

```xml
mac:app-inspector mac$ npm run build
mac:app-inspector mac$ ./bin/app-inspector -u DU2MYN146D002912
```

## 参考来源
https://github.com/alibaba/macaca/issues/228
