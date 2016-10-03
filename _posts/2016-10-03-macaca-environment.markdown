---
layout: post
category: "macaca"
title:  "macaca环境搭建"
tags: [macaca,nodejs]
---

# macaca环境搭建

###  软件安装前提
```xml
1.JAVA环境
2.安卓环境（如果需要测试安卓设备）
3.nodejs   
4.cnpm
```

###  前提软件安装

1.JAVA环境安装，一路安装
![14754839526711-w1230](../assets/14754839526711.jpg)

2.设置JAVA环境变量

```xml
mac:~ mac$ sudo vi /etc/profile 
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home
export CLASS_PATH=$JAVA_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$CLASS_PATH

mac:~ mac$ source /etc/profile 
```
3.[下载Android sdk](https://dl.google.com/android/android-sdk_r24.4.1-macosx.zip),解压后进入tools目录下，打开Android程序出现下面截图，勾选并进行安装

![](../assets/14754873177417.jpg)

4.安装完成后，配置android环境变量
```xml
export ANDROID_HOME=/Users/mac/software/android-sdk-macosx
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools

mac:~ mac$ source /etc/profile 
```
5.安装nodejs,一路下一步
![](../assets/14754875638447.jpg)

6.安装cnpm国内镜像
```xml
mac:~ mac$ npm install -g cnpm --registry=https://registry.npm.taobao.org
npm ERR! Darwin 15.6.0
npm ERR! argv "/usr/local/bin/node" "/usr/local/bin/npm" "install" "-g" "cnpm" "--registry=https://registry.npm.taobao.org"
npm ERR! node v4.5.0
npm ERR! npm  v2.15.9
npm ERR! path /usr/local/lib/node_modules/cnpm
npm ERR! code EACCES
npm ERR! errno -13
npm ERR! syscall rmdir
npm ERR! Error: EACCES: permission denied, rmdir '/usr/local/lib/node_modules/cnpm'
npm ERR!     at Error (native)
npm ERR!  { [Error: EACCES: permission denied, rmdir '/usr/local/lib/node_modules/cnpm']
npm ERR!   errno: -13,
npm ERR!   code: 'EACCES',
npm ERR!   syscall: 'rmdir',
npm ERR!   path: '/usr/local/lib/node_modules/cnpm' }
npm ERR! 
npm ERR! Please try running this command again as root/Administrator.
npm ERR! error rolling back Error: EACCES: permission denied, rmdir '/usr/local/lib/node_modules/cnpm'
npm ERR! error rolling back     at Error (native)
npm ERR! error rolling back  { [Error: EACCES: permission denied, rmdir '/usr/local/lib/node_modules/cnpm']
npm ERR! error rolling back   errno: -13,
npm ERR! error rolling back   code: 'EACCES',
npm ERR! error rolling back   syscall: 'rmdir',
npm ERR! error rolling back   path: '/usr/local/lib/node_modules/cnpm' }
npm ERR! Please include the following file with any support request:
npm ERR!     /Users/mac/npm-debug.log

```
出现上面报错时候可以进行下面操作
```xml
mac:~ mac$ sudo mkdir -p /usr/local/lib/node_modules/cnpm
mac:~ mac$ sudo chown -R mac /usr/local/lib/node_modules/
mac:~ mac$ sudo chown -R mac /usr/local/bin/
```
### macaca相关软件安装
1.安装macaca服务端程序

```xml
npm install macaca-cli -g
```
2.如果需要测试安卓设备
```xml
cnpm install macaca-android -g
```
3.测试浏览器需要
```xml
cnpm install macaca-chrome -g
```
4.测试ios设备需要安装
* 首先需要安装homebrew
```xml
mac:~ mac$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
* 安装ios代理
```xml
brew install ios-webkit-debug-proxy
```
* 安装IOS驱动
```xml
cnpm install macaca-ios -g
```
5.测试电脑端需要安装
```xml
cnpm install macaca-electron -g
```
6.如果想通过页面审查设备元素，可以安装app-inspector
```xml
cnpm install app-inspector -g
```
![审查页面截图](../assets/14754890192127.jpg)

7.检查环境是否成功
```xml
mac:~ mac$ macaca doctor
  macaca-doctor version: 1.0.19
  Node.js checklist:
  node env: /usr/local/bin/node
  node version: v4.5.0
  iOS checklist:
  Xcode is installed at: `/Applications/Xcode.app/Contents/Developer`
  Xcode Command Line Tools is ready, version: 2343.
  ios_webkit_debug_proxy is installed at: /usr/local/bin/ios_webkit_debug_proxy
  Android checklist:
  JAVA version is `1.8.0_91`
  JAVA_HOME is set to `/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home`
  ANDROID_HOME is set to `/Users/mac/software/android-sdk-macosx`
  Platforms is set to `/Users/mac/software/android-sdk-macosx/platforms/android-24`
  Android tools is set to `/Users/mac/software/android-sdk-macosx/tools/android`
  ADB tool is set to `/Users/mac/software/android-sdk-macosx/platform-tools/adb`
  Installed driver list:
  android: 1.0.28
  chrome: 1.0.2
  electron: 1.1.2
  ios: 1.0.37
```
8.打开xcode->proferences找到下面的accounts,并登陆开发者账号
![登录开发者帐号](../assets/14755022998321.jpg)

![下载所有证书](../assets/14755023121490.jpg)


用xcode打开/usr/local/lib/node_modules/.app-inspector_npminstall/node_modules/.1.0.28@webdriveragent/WebDriverAgent/WebDriverAgent.xcodeproj

![选择证书](../assets/14755023314919.jpg)

![选择Lib](../assets/14755023396800.jpg)

![选择runner](../assets/14755023488113.jpg)


![给runner选择team](../assets/14755023582886.jpg)

![修改bundle id](../assets/14755023656775.jpg)

9.Product->build

```xml
mac:macaca-test-sample mac$ idevice_id -l
9daef85429600e88ea5442ba3d5fee548c02aeac
```

```xml
mac:macaca-test-sample mac$ inspector -u 9daef85429600e88ea5442ba3d5fee548c02aeac --verbose

```



