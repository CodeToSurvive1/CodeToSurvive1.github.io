# macaca环境搭建

## 1.基础环境配置

1.安装nodejs

下载地址：[https://nodejs.org/en/](https://nodejs.org/en/)  
备用下载地址：[http://cdn.npm.taobao.org/dist/node/v6.9.4/node-v6.9.4-darwin-x64.tar.xz](http://cdn.npm.taobao.org/dist/node/v6.9.4/node-v6.9.4-darwin-x64.tar.xz)  
下载版本：V6.9.4

2.安装nodejs

在用户主目录下创建macaca目录

```bash
mkdir -p /Users/admin/macaca
```

切换到下载的nodejs目录下执行下面命令将其解压到macaca目录下

```bash
tar -zxvf node-v6.9.4-darwin-x64.tar.xz -C ~/macaca/
```

配置环境变量

```bash
sudo vi ~/.bash_profile 
```

添加下面内容

```bash
#nodejs path
NODEJS_HOME=/Users/admin/macaca/node-v6.9.4-darwin-x64
PATH=$PATH:$NODEJS_HOME/bin
```

最后不要忘记source

```
source ~/.bash_profile 
```

验证node是否安装成功

```
node
> 
```

3.安装cnpm

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

4.安装macaca-cli

```bash
npm install macaca-cli -g
```

---

## 安卓环境配置

1.安装java环境jdk

下载地址[http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

这里下载最新版本 jdk-8u121-macosx-x64.dmg

直接安装，安装完成后，配置环境变量

```bash
sudo vi ~/.bash_profile  
```

添加如下内容

```
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home
JAVA_CLASSPATH=$JAVA_HOME/lib
PATH=$JAVA_HOME/bin:$PATH:$JAVA_CLASSPATH
```

最后不要忘记source

```
source ~/.bash_profile 
```

验证java是否安装成功

```
java

javac

java -version
```

2.下载ant [http://ant.apache.org/bindownload.cgi](http://ant.apache.org/bindownload.cgi)  
这里下载最新版本  
[http://mirrors.hust.edu.cn/apache//ant/binaries/apache-ant-1.10.1-bin.tar.gz](http://mirrors.hust.edu.cn/apache//ant/binaries/apache-ant-1.10.1-bin.tar.gz)

下载后进入下载目录后执行下面命令将其解压到macaca目录下

```
tar -zxvf apache-ant-1.10.1-bin.tar -C ~/macaca/
```

配置ant环境变量

```
sudo vi ~/.bash_profile 
```

添加下面配置

```
#ant home
ANT_HOME=/Users/admin/macaca/apache-ant-1.10.1
PATH=$PATH:$ANT_HOME/bin
```

最后执行source

```
 source ~/.bash_profile  
```

验证ant是否安装成功

```
ant -version
Apache Ant(TM) version 1.10.1 compiled on February 2 2017
```

3.安装android sdk

下载android sdk [http://tools.android-studio.org/index.php/sdk下载最新版本](http://tools.android-studio.org/index.php/sdk下载最新版本)

下载后解压并配置环境变量

```
export ANDROID_HOME=$HOME/mlab/android-sdk-macosx
export PATH="$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$PATH"
```

验证是否安装正确

```
android
```

弹出android sdk manager管理页面，然后下载最新的sdk及最新的tools

然后下载macaca的android驱动

```
npm install macaca-android -g
```

4.如果想测试android的webview需要安装chrome驱动程序

```
npm install macaca-chrome -g
```

最后通过macaca doctor检测android是否安装成功

     macaca doctor

      macaca-doctor version: 1.0.28


      Node.js checklist:

      node env: /Users/admin/macaca/node-v6.9.4-darwin-x64/bin/node
      node version: v6.9.4

      iOS checklist:

      Xcode is installed at: `/Applications/Xcode.app/Contents/Developer`
      Xcode Command Line Tools is ready, version: 2343.
      iproxy[usbmuxd] is installed at: `/usr/local/bin/iproxy`
      ios_webkit_debug_proxy is installed at: `/usr/local/bin/ios_webkit_debug_proxy`

      Android checklist:

      JAVA version is `1.8.0_121`
      JAVA_HOME is set to `/Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home`
      ANDROID_HOME is set to `/Users/admin/mlab/android-sdk-macosx`
      Platforms is set to `/Users/admin/mlab/android-sdk-macosx/platforms/android-24`
      Android tools is set to `/Users/admin/mlab/android-sdk-macosx/tools/android`
      ADB tool is set to `/Users/admin/mlab/android-sdk-macosx/platform-tools/adb`

      Installed driver list:

      android: 1.1.20
      chrome: 1.0.5
      ios: 1.0.53

---

## ios环境搭建

1.安装最新的xcode

2.安装brew

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

3.更新brew源

```
brew update
```

4.如果要测试webview需要安装ios-webkit-debug-proxy

```
brew install ios-webkit-debug-proxy
```

5.安装usb驱动

```
brew install usbmuxd
```

6.安装macaca的ios驱动

```
npm install macaca-ios -g
```

7.最后执行macaca doctor检测下环境是否ok

    macaca doctor

      macaca-doctor version: 1.0.28


      Node.js checklist:

      node env: /Users/admin/macaca/node-v6.9.4-darwin-x64/bin/node
      node version: v6.9.4

      iOS checklist:

      Xcode is installed at: `/Applications/Xcode.app/Contents/Developer`
      Xcode Command Line Tools is ready, version: 2343.
      iproxy[usbmuxd] is installed at: `/usr/local/bin/iproxy`
      ios_webkit_debug_proxy is installed at: `/usr/local/bin/ios_webkit_debug_proxy`

      Android checklist:

      JAVA version is `1.8.0_121`
      JAVA_HOME is set to `/Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home`
      ANDROID_HOME is set to `/Users/admin/mlab/android-sdk-macosx`
      Platforms is set to `/Users/admin/mlab/android-sdk-macosx/platforms/android-24`
      Android tools is set to `/Users/admin/mlab/android-sdk-macosx/tools/android`
      ADB tool is set to `/Users/admin/mlab/android-sdk-macosx/platform-tools/adb`

      Installed driver list:

      android: 1.1.20
      ios: 1.0.53

---

## electron环境搭建

```
npm install macaca-electron -g
```

检查是否安装成功

    macaca doctor

      macaca-doctor version: 1.0.28


      Node.js checklist:

      node env: /Users/admin/macaca/node-v6.9.4-darwin-x64/bin/node
      node version: v6.9.4

      iOS checklist:

      Xcode is installed at: `/Applications/Xcode.app/Contents/Developer`
      Xcode Command Line Tools is ready, version: 2343.
      iproxy[usbmuxd] is installed at: `/usr/local/bin/iproxy`
      ios_webkit_debug_proxy is installed at: `/usr/local/bin/ios_webkit_debug_proxy`

      Android checklist:

      JAVA version is `1.8.0_121`
      JAVA_HOME is set to `/Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home`
      ANDROID_HOME is set to `/Users/admin/mlab/android-sdk-macosx`
      Platforms is set to `/Users/admin/mlab/android-sdk-macosx/platforms/android-24`
      Android tools is set to `/Users/admin/mlab/android-sdk-macosx/tools/android`
      ADB tool is set to `/Users/admin/mlab/android-sdk-macosx/platform-tools/adb`

      Installed driver list:

      android: 1.1.20
      chrome: 1.0.5
      electron: 1.1.5
      ios: 1.0.53

---

## 案例运行测试

### electron测试

1.下载desktop-browser-sample-nodejs.git

```
git clone https://github.com/macaca-sample/desktop-browser-sample-nodejs.git
Cloning into 'desktop-browser-sample-nodejs'...
remote: Counting objects: 39, done.
remote: Total 39 (delta 0), reused 0 (delta 0), pack-reused 39
Unpacking objects: 100% (39/39), done.

cd desktop-browser-sample-nodejs
```

2.切换进去目录后安装依赖

```
npm i
```

3.执行测试

```
macaca run -d macaca-test/desktop-browser-sample-nodejs.test.js --verbose
```

### 手机测试

1.下载源代码

```
git clone https://github.com/macaca-sample/mobile-app-sample-nodejs.git
Cloning into 'mobile-app-sample-nodejs'...
remote: Counting objects: 73, done.
remote: Total 73 (delta 0), reused 0 (delta 0), pack-reused 73
Unpacking objects: 100% (73/73), done.

cd mobile-app-sample-nodejs
```

2.安装依赖

```
npm i
```

3.执行脚本

```
macaca run -d macaca-test/mobile-app-sample.test.js --verbose
```

默认是运行的ios模拟器，这里也可以选择真机测试

---



