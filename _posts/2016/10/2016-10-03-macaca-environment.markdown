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
brew install usbmuxd 
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

8.运行脚本过程中报错或者inspector过程中报错，打开xcode->proferences找到下面的accounts,并登陆开发者账号    

比如，安装完app-inspector通过inspector查看后台报错  


```xml

插入ios真机设备，查看序列号

idevice_id -l
9daef85429600e88ea5442ba3d5fee548c02aeac

启动inspector

inspector -u 9daef85429600e88ea5442ba3d5fee548c02aeac --verbose
>> request.js:24:12 [master] pid:3167 get remote update info failed.
>> server.js:16:12 [master] pid:3167 server start with config:
 { port: 5678,
  verbose: true,
  udid: '9daef85429600e88ea5442ba3d5fee548c02aeac',
  ip: '192.168.31.164',
  host: 'mac.local',
  loaded_time: '2016-11-07 21:01:21' }
>> app-inspector.js:42:10 [master] pid:3167 server start at: http://192.168.31.164:5678
>> xctest-client.js:46:14 [master] pid:3167 project path: /usr/local/lib/node_modules/.app-inspector_npminstall/node_modules/.1.0.28@webdriveragent/WebDriverAgent/WebDriverAgent.xcodeproj
>> xctest-client.js:215:12 [master] pid:3167 xcode version: 8.1
>> WebDriverAgent version: 1.0.28
>> xctest-client.js:160:14 [master] pid:3167 2016-11-07 21:01:24.258 xcodebuild[3174:78023]  IDETestOperationsObserverDebug: Writing diagnostic log for test session to:
/Users/mac/Library/Developer/Xcode/DerivedData/WebDriverAgent-bigkcildozwtjqcpfxzovxskdqza/Logs/Test/79C8FCCF-B531-40EC-85B8-59D11FA5B4FA/Session-WebDriverAgentRunner-2016-11-07_210124-LVSTWc.log

>> xctest-client.js:160:14 [master] pid:3167 2016-11-07 21:01:24.258 xcodebuild[3174:78017] [MT] IDETestOperationsObserverDebug: (168B0761-A302-4A92-908A-01BD523994ED) Beginning test session WebDriverAgentRunner-168B0761-A302-4A92-908A-01BD523994ED at 2016-11-07 21:01:24.257 with Xcode 8B62 on target <DVTiOSDevice: 0x7fbf64f26870> {
    deviceSerialNumber:         FK2P1TWEG5QY
    identifier:                 9daef85429600e88ea5442ba3d5fee548c02aeac
    deviceClass:                iPhone
    deviceName:                 李小蛟的 iPhone
    deviceIdentifier:           9daef85429600e88ea5442ba3d5fee548c02aeac
    productVersion:             10.1.1
    buildVersion:               14B100
    deviceSoftwareVersion:      10.1.1 (14B100)
    deviceArchitecture:         arm64
    deviceTotalCapacity:        58959704064
    deviceAvailableCapacity:    44852158464
    deviceIsTransient:          NO
    ignored:                    NO
    deviceIsBusy:               NO
    deviceIsActivated:          YES
    deviceActivationState:      Activated
    deviceType:                 <DVTDeviceType:0x7fbf64bb3650 Xcode.DeviceType.iPhone>
    supportedDeviceFamilies:    (
    1
)
    applications:              (null)
    provisioningProfiles:      (null)
    activityProgress:          -2
    activityTitle:             
    hasInternalSupport:        NO
    isSupportedOS:             YES
    developerDiskMountError:   (null)
(null)
  } (10.1.1 (14B100))

>> xctest-client.js:160:14 [master] pid:3167 2016-11-07 21:01:30.215 xcodebuild[3174:78017] Error Domain=IDETestOperationsObserverErrorDomain Code=3 "Test operation was canceled. If you believe this error represents a bug, please attach the log file at /Users/mac/Library/Developer/Xcode/DerivedData/WebDriverAgent-bigkcildozwtjqcpfxzovxskdqza/Logs/Test/79C8FCCF-B531-40EC-85B8-59D11FA5B4FA/Session-WebDriverAgentRunner-2016-11-07_210124-LVSTWc.log" UserInfo={NSLocalizedDescription=Test operation was canceled. If you believe this error represents a bug, please attach the log file at /Users/mac/Library/Developer/Xcode/DerivedData/WebDriverAgent-bigkcildozwtjqcpfxzovxskdqza/Logs/Test/79C8FCCF-B531-40EC-85B8-59D11FA5B4FA/Session-WebDriverAgentRunner-2016-11-07_210124-LVSTWc.log}

>> xctest-client.js:160:14 [master] pid:3167 
Testing failed:

>> xctest-client.js:160:14 [master] pid:3167  Signing for "WebDriverAgentRunner" requires a development team. Select a development team in the project editor.
  Code signing is required for product type 'UI Testing Bundle' in SDK 'iOS 10.1'
** TEST FAILED **


The following build commands failed:
  Check dependencies
(1 failure)

>> xctest-client.js:168:14 [master] pid:3167 xctest client exit with code: 65, signal: null


```

![路径](../assets/2016/11/2016-11-07_21-05-39.png)

可以看到上面的提示信息是需要选择开发帐号,路径也能在上面找到/usr/local/lib/node_modules/.app-inspector_npminstall/node_modules/.1.0.28@webdriveragent/WebDriverAgent/WebDriverAgent.xcodeproj       

用xcode打开/usr/local/lib/node_modules/.app-inspector_npminstall/node_modules/.1.0.28@webdriveragent/WebDriverAgent/WebDriverAgent.xcodeproj，请对应上面的报错的日志中输出的路径打开,按照下面步骤进行修改,修改后再次运行命令即可     

![登录开发者帐号](../assets/14755022998321.jpg)

![下载所有证书](../assets/14755023121490.jpg)




![选择证书](../assets/14755023314919.jpg)

![选择Lib](../assets/14755023396800.jpg)

![选择runner](../assets/14755023488113.jpg)


![给runner选择team](../assets/14755023582886.jpg)

![修改bundle id](../assets/14755023656775.jpg)

同样，进行脚本运行的时候也可能会出现错误，比如下面跑脚本报错,   

```xml

mac:changshutest mac$ macaca run -d js/weixin.js --verbose
>> request.js:24:12 [master] pid:4046 get remote update info failed.
>> index.js:17:12 [master] pid:4047 webdriver server start with config:
 { port: 3456,
  verbose: false,
  always: true,
  window: true,
  ip: '192.168.31.164',
  host: 'mac.local',
  loaded_time: '2016-11-07 21:24:47' }
>> middlewares.js:17:10 [master] pid:4047 base middlewares attached
>> router.js:129:10 [master] pid:4047 router set
>> webdriver sdk launched
>> 

>> 

>>   macaca mobile sample

>> responseHandler.js:11:12 [master] pid:4047 Recieve HTTP Request from Client: method: POST url: /wd/hub/session, jsonBody: {"desiredCapabilities":{"autoAcceptAlerts":true,"platformName":"iOS","platformVersion":"9.3.4","deviceName":"iPhone6 plus","udid":"9daef85429600e88ea5442ba3d5fee548c02aeac","bundleId":"com.tencent.xin"}}
>> session.js:47:10 [master] pid:4047 Creating session, sessionId: 52c618f1-79c3-49bb-ac3d-ef78e3cb8581.
>> xctest-client.js:46:14 [master] pid:4047 project path: /usr/local/lib/node_modules/.macaca-ios_npminstall/node_modules/.1.0.28@webdriveragent/WebDriverAgent/WebDriverAgent.xcodeproj
>> macaca-ios.js:144:10 [master] pid:4047 {
  "bundleId": "com.tencent.xin",
  "platformName": "iOS",
  "platformVersion": "9.3.4"
}
>> xctest-client.js:215:12 [master] pid:4047 xcode version: 8.1
>> WebDriverAgent version: 1.0.28
>> xctest-client.js:160:14 [master] pid:4047 2016-11-07 21:24:49.160 xcodebuild[4058:91967]  IDETestOperationsObserverDebug: Writing diagnostic log for test session to:
/Users/mac/Library/Developer/Xcode/DerivedData/WebDriverAgent-bagqeermmcadmcctywbyvbqewyeo/Logs/Test/8AA0F0C9-B54C-4C20-AFB4-175215CAC0C1/Session-WebDriverAgentRunner-2016-11-07_212449-z5pAjR.log

>> xctest-client.js:160:14 [master] pid:4047 2016-11-07 21:24:49.160 xcodebuild[4058:91966] [MT] IDETestOperationsObserverDebug: (D8B50A66-C013-4DE1-B715-2839FD6DF5D2) Beginning test session WebDriverAgentRunner-D8B50A66-C013-4DE1-B715-2839FD6DF5D2 at 2016-11-07 21:24:49.159 with Xcode 8B62 on target <DVTiOSDevice: 0x7fde1135db10> {
    deviceSerialNumber:         FK2P1TWEG5QY
    identifier:                 9daef85429600e88ea5442ba3d5fee548c02aeac
    deviceClass:                iPhone
    deviceName:                 李小蛟的 iPhone
    deviceIdentifier:           9daef85429600e88ea5442ba3d5fee548c02aeac
    productVersion:             10.1.1
    buildVersion:               14B100
    deviceSoftwareVersion:      10.1.1 (14B100)
    deviceArchitecture:         arm64
    deviceTotalCapacity:        58959704064
    deviceAvailableCapacity:    44848533504
    deviceIsTransient:          NO
    ignored:                    NO
    deviceIsBusy:               NO
    deviceIsActivated:          YES
    deviceActivationState:      Activated
    deviceType:                 <DVTDeviceType:0x7fde10f31c10 Xcode.DeviceType.iPhone>
    supportedDeviceFamilies:    (
    1
)
    applications:              (null)
    provisioningProfiles:      (null)
    activityProgress:          -2
    activityTitle:             
    hasInternalSupport:        NO
    isSupportedOS:             YES
    developerDiskMountError:   (null)
(null)
  } (10.1.1 (14B100))

>> xctest-client.js:160:14 [master] pid:4047 2016-11-07 21:24:51.911 xcodebuild[4058:91966] Error Domain=IDETestOperationsObserverErrorDomain Code=3 "Test operation was canceled. If you believe this error represents a bug, please attach the log file at /Users/mac/Library/Developer/Xcode/DerivedData/WebDriverAgent-bagqeermmcadmcctywbyvbqewyeo/Logs/Test/8AA0F0C9-B54C-4C20-AFB4-175215CAC0C1/Session-WebDriverAgentRunner-2016-11-07_212449-z5pAjR.log" UserInfo={NSLocalizedDescription=Test operation was canceled. If you believe this error represents a bug, please attach the log file at /Users/mac/Library/Developer/Xcode/DerivedData/WebDriverAgent-bagqeermmcadmcctywbyvbqewyeo/Logs/Test/8AA0F0C9-B54C-4C20-AFB4-175215CAC0C1/Session-WebDriverAgentRunner-2016-11-07_212449-z5pAjR.log}

>> xctest-client.js:160:14 [master] pid:4047 
Testing failed:

>> xctest-client.js:160:14 [master] pid:4047  Signing for "WebDriverAgentRunner" requires a development team. Select a development team in the project editor.
  Code signing is required for product type 'UI Testing Bundle' in SDK 'iOS 10.1'
** TEST FAILED **


The following build commands failed:
  Check dependencies
(1 failure)

>> xctest-client.js:168:14 [master] pid:4047 xctest client exit with code: 65, signal: null

``` 

一样从上面找到   

![](../assets/2016/11/2016-11-07_21-27-01.png)  

同样按照上面的步骤进行操作，操作后再次运行即可   

9.Product->build

```xml
mac:macaca-test-sample mac$ idevice_id -l
9daef85429600e88ea5442ba3d5fee548c02aeac
```

```xml
mac:macaca-test-sample mac$ inspector -u 9daef85429600e88ea5442ba3d5fee548c02aeac --verbose

```
