---
layout: "post"
category: "macaca"
title: "macaca源码分析-android-uiautomator"
date: "2016-10-05 07:54"
---


```xml
  由于新手接触自动化来说，很多知识需要恶补，其中对于自动化脚本是如何通过指令发送到手机，感觉很是神奇，因此对于macaca中如何通过脚本操控手机这块进行了相关的研究，发现，在macaca中，操控安卓手机主要有两种方式：
  1.  直接对底层adb指令进行封装，上层进行相关调用，比如截图啊、查询设备啊之类
  2.  通过安卓自动化框架封装的Uiautomator进行相关封装
  本文主要分析macaca中对于Uiautomator的封装的讨论
```
***

#### 前提准备

```xml
  1.  macaca-adb
  2.  macaca-android
  3.  uiautomator-client
```
***

#### 软件下载方式

可以通过两种方式获取：

```xml
    1.  安装macaca-cli及android驱动，[可以参考macaca环境搭建](https://codetosurvive1.github.io/posts/macaca-environment.html)，安装成功后会在对应的目录下生成node_moduels下,可以直接查看/usr/local/lib/node_modules/macaca-android驱动，该驱动依赖于macaca-adb以及uiautomator-client
    2.  源码方式查看，直接从github下载源代码并安装相关依赖包
```
***

#### 软件下载
  这里直接使用上面的第二种方式，源码下载安装方式查看

```xml
    1.下载源代码
      mac:macaca mac$ git clone https://github.com/macacajs/macaca-android.git

      Cloning into 'macaca-android'...
      remote: Counting objects: 337, done.
      remote: Compressing objects: 100% (35/35), done.
      remote: Total 337 (delta 22), reused 0 (delta 0), pack-reused 302
      Receiving objects: 100% (337/337), 54.47 KiB | 64.00 KiB/s, done.
      Resolving deltas: 100% (222/222), done.
      Checking connectivity... done.
    2.安装依赖
      mac:macaca mac$ cd macaca-android/
      mac:macaca-android mac$ npm install
    3.这里使用webstorm打开macaca-android工程
```
  webstorm中工程截图如下

  ![工程截图](../assets/14756299103045.jpg)

***
#### 源码分析

  macaca-android代码目录lib下核心代码为controllers.js和macaca-android.js

  macaca-android.js中构造函数  

```js
class Android extends DriverBase {
  constructor() {
    super();
    this.adb = null;
    this.apkInfo = null;
    this.args = null;
    this.chromedriver = null;
    this.chromeDriverPort = null;
    this.proxy = null;
    this.udid = null;
    this.uiautomator = null;
    this.isChrome = null;
    this.isVirtual = true;
    this.moveToPosition = null;
    this.contexts = [];
  }
}
```

  启动设备

```js

Android.prototype.startDevice = function *(caps) {
  this.args = _.clone(caps);
  this.isChrome = this.args.browserName && this.args.browserName.toLowerCase() === 'chrome';
  yield JAVA.getVersion();
  this.initReuse();
  this.initAdb();
  yield this.initDevice();
  yield this.initUiautomator();//初始化UIautomator
  yield this.getApkInfo();
  yield this.unlock();
  yield this.setIME();
  yield this.launchApk();
  yield this.waitActivityReady();

  if (this.isChrome) {
    yield this.getWebviews();
  }
};
```

```js
Android.prototype.initUiautomator = function *() {
  this.uiautomator = new UIAutomator();
  yield this.uiautomator.init(this.adb);
};
```

  UIAutomator引入包uiautomator-client  

```js
  const UIAutomator = require('uiautomator-client');    
```

  查看uiautomator-client包下lib下的uiautomator-client.js  

  UIAutomator的构造函数及init方法

```js
function UIAutomator() {
  this.adb = null;
  this.socket = null;
  this.socketPort = 6789;
  this.queue = [];
}
```

```js
UIAutomator.prototype.init = function *(adb) {
  this.adb = adb;
  const ANDROID_TMP_DIR = this.adb.getTmpDir();

  try {
    yield this.adb.rm(`${ANDROID_TMP_DIR}/${FILE_NAME}.jar`);
  } catch (e) {

  }
  if (!_.isExistedFile(binPath)) {
    logger.error(`uiautomator-bootstrap was not found in: ${binPath}, please check your Android ENV`);
    return;
  }
  yield this.adb.push(binPath, ANDROID_TMP_DIR);
  try {
    yield this.adb.killProcess('uiautomator');
  } catch (e) {

  }
  yield this.adb.forward(this.socketPort, this.socketPort);
  yield this.initSocketServer();
  yield this.initSocketClient();
};
```
  从上面的构造函数及init方法中可以看出，默认构造了6789端口，然后调用init初始化方法中，首先删除了安卓手机临时目录'/data/local/tmp'中的uiautomator-bootstrap.jar包,然后上传jar包到临时目录下，并杀掉手机中的uiautomator进程，并将手机端口与电脑端口进行映射，最后初始化手机端的服务器及电脑端的客户端连接。

```js
UIAutomator.prototype.initSocketServer = function() {
  return new Promise((resolve, reject) => {
    const ANDROID_TMP_DIR = this.adb.getTmpDir();
    let args = `shell uiautomator runtest ${ANDROID_TMP_DIR}/${FILE_NAME}.jar -c ${CLASS_NAME} -e port ${this.socketPort} -e flag ${READY_FLAG}`.split(' ');

    var proc = this.adb.spawn(args, {
      path: process.cwd(),
      env: process.env
    });

    proc.stderr.setEncoding('utf8');
    proc.stdout.setEncoding('utf8');
    proc.stdout.on('data', data => {
      if (!!~data.indexOf(READY_FLAG)) {
        logger.info('socket server ready');
        resolve();
      } else {
        console.log(data);
      }
    });
    proc.stderr.on('data', data => {
      console.log(data);
    });
  });
};
```
  初始化手机服务端方法中，可以看出是直接调用通过adb shell调用手机中的uiautomator执行uiautomator的测试集adb shell uiautomator runtest ${ANDROID_TMP_DIR}/${FILE_NAME}.jar -c ${CLASS_NAME} -e port ${this.socketPort} -e flag ${READY_FLAG}，这里的${ANDROID_TMP_DIR}/${FILE_NAME}.jar指的是/data/local/tmp/uiautomator-bootstrap.jar,-c指定要运行的测试类，即为com.android.uiautomator.client.Initialize，-e指定参数，这里指定了两个参数，一个是port，一个是flag

```js
UIAutomator.prototype.initSocketClient = function() {
  return new Promise((resolve, reject) => {
    this.socket = net.connect(this.socketPort, () => {
      logger.info('socket client ready');
      resolve();
    });

    var tempData = '';
    this.socket.setEncoding('utf8');
    this.socket.on('data', data => {
      try {
        let res = JSON.parse(tempData + data);
        tempData = '';
        let success = res.success;

        if (success) {
          this.queue.shift().resolve(res.data);
        } else {
          this.queue.shift().reject();
        }
      } catch (e) {
        tempData += data;
      }
    });
    this.socket.on('end', () => {
      logger.warn('connect lost');
    });
  });
};
```

  电脑客户端连接比较简单，直接连接上服务端，并注册监听事件。

```js
UIAutomator.prototype.send = function *(cmd) {
  var defer = new _.Defer();
  defer.promise.then(data => {
  });
  this.queue.push(defer);
  this.socket.write(`${JSON.stringify(cmd)}\n`);
  return yield defer.promise;
};
```
  这是另外的一个发送的方法send，直接将指令发送到服务端,该方法的调用在macaca-android.js中,这里很简单直接调用并返回响应码  

```js

Android.prototype.send = function *(data) {
  try {
    var result = yield this.uiautomator.send(data);
  } catch (err) {
    logger.debug(`UnknownError from uiautomator ${err}`);
    throw new errors.UnknownError();
  }
  var statusCode = result.status;
  if (statusCode === 0) {
    return result.value;
  } else {
    var errName = getErrorByCode(statusCode);
    if (!errName) {
      throw new errors.UnknownError();
    }
    throw new errors[errName](result.value);
  }
};
```  

***

#### 手机端uiautomator源码导入

![uiautomator源码](../assets/14756529485819.jpg)  

  从这里的目录可以看出macaca中对于uiautomator的java源代码封装，下面使用idea进行相关源码导入

  1.建立普通java工程
![idea建立java](../assets/14756532355705.jpg)
![defalut](../assets/14756534264572.jpg)
![finish](../assets/14756534388076.jpg)
![uiautomator-client](../assets/14756534452375.jpg)

  2.uiautomator-client目录下的src拷贝到创建的java工程中  
![copy](../assets/14756534526096.jpg)
![copyfinish](../assets/14756534607405.jpg)

  3.添加uiatuomator，android，及fastjson的jar包,添加成功后，代码中不报错  
![dependency](../assets/14756534667998.jpg)
![uiatuomatorjar](../assets/14756534732561.jpg)
![android](../assets/14756534789121.jpg)
![fastjson](../assets/14756534845583.jpg)
![](../assets/14756534904259.jpg)
![](../assets/14756534969506.jpg)

***

#### 手机端uiautomator源码分析

  1.直接查看上面js中指定的com.android.uiautomator.client.Initialize源码

```java
package com.android.uiautomator.client;

import com.android.uiautomator.testrunner.UiAutomatorTestCase;
import org.json.JSONException;

import javax.xml.parsers.ParserConfigurationException;
import java.io.IOException;

/**
 * @author xdf
 *
 */
public class Initialize extends UiAutomatorTestCase {

	/**
	 * @throws IOException
	 * @throws JSONException
	 * @throws ParserConfigurationException
	 */
	public void testStartServer() throws IOException, JSONException,
			ParserConfigurationException {
		Utils.output("uiautomator start socket server.");
		int port = Integer.parseInt(getParams().getString("port"));
		String readyFlag = getParams().getString("flag");
		SocketServer server = new SocketServer(port);
		server.listen(readyFlag);
	}
}
```

  可以看出，该类直接继承自uiautomator框架中的UiAutomatorTestCase，该方法直接获取传递进来的参数port和flag，并传递给SocketServer类  

  2.SocketServer类是简单的java tcp服务端实现类

```java
package com.android.uiautomator.client;

import org.json.JSONException;

import javax.xml.parsers.ParserConfigurationException;
import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * @author xdf
 *
 */
public class SocketServer {
	/**
	 *
	 */
	private ServerSocket server;

	/**
	 * @author xdf
	 * @param port
	 * @throws IOException
	 *
	 */

	public SocketServer(int port) throws IOException {
		setServer(new ServerSocket(port));
	}

	/**
	 * @param readyFlag
	 * @throws JSONException
	 * @throws ParserConfigurationException
	 */
	public void listen(String readyFlag) throws JSONException,
			ParserConfigurationException {
		Utils.output(readyFlag);
		try {
			Socket client = server.accept();
			BufferedReader input = new BufferedReader(new InputStreamReader(
					client.getInputStream(), "UTF-8"));
			BufferedWriter output = new BufferedWriter(new OutputStreamWriter(
					client.getOutputStream(), "UTF-8"));
			StringBuilder swap = new StringBuilder();

			while (true) {
				swap.setLength(0);
				int chunk;
				while ((chunk = input.read()) != -1 && input.ready()) {
					swap.append((char) chunk);
				}
				String str = swap.toString();
				Utils.output("recive: " + str);
				String res = Command.handleInput(str);
				Utils.output("return: " + res);
				output.write(res);
				output.flush();
			}
		} catch (final IOException e) {

		}
	}

	/**
	 * @return res
	 */

	public ServerSocket getServer() {
		return server;
	}

	/**
	 * @param server
	 */
	public void setServer(ServerSocket server) {
		this.server = server;
	}
}
```

  该类的核心代码在listen方法，该方法即为死循环，不停的接受发送过来的指令，然后处理执行Command.handleInput，并将处理的结果返回  

  3.Command.handleInput处理接受到的指令并解析，并根据命令将命令参数传递给不同的实现类  

```java
package com.android.uiautomator.client;

import com.android.uiautomator.client.cmd.*;
import org.json.JSONException;
import org.json.JSONObject;

import javax.xml.parsers.ParserConfigurationException;
import java.util.HashMap;

/**
 * @author xdf
 *
 */
public class Command {
	/**
	 *
	 */
	private static HashMap<String, CommandBase> map = new HashMap<String, CommandBase>();

	static {
		getMap().put("ping", new Ping());
		getMap().put("wake", new Wake());
		getMap().put("find", new Find());
		getMap().put("setText", new SetText());
		getMap().put("getText", new GetText());
		getMap().put("click", new Click());
		getMap().put("clearText", new ClearText());
		getMap().put("swipe", new Swipe());
		getMap().put("getWindowSize", new GetWindowSize());
		getMap().put("getProperties", new GetProperties());
		getMap().put("getSource", new GetSource());
	}

	/**
	 * @param input
	 * @return res
	 * @throws JSONException
	 * @throws ParserConfigurationException
	 */
	static String handleInput(String input) throws JSONException,
			ParserConfigurationException {
		JSONObject json = Utils.parseJSON(input);
		String cmd = (String) json.get("cmd");
		JSONObject args = (JSONObject) json.get("args");
		return getMap().get(cmd).execute(args);
	}

	/**
	 * @return res
	 */
	public static HashMap<String, CommandBase> getMap() {
		return map;
	}

	/**
	 * @param map
	 */
	public static void setMap(HashMap<String, CommandBase> map) {
		Command.map = map;
	}
}
```

  其中的handleInput即接受指令，然后从指令中获取命令和命令参数，并调用getMap().get(cmd).execute(args)，而getMap()中的map的初始化在static域中  


  | 命令 | 处理类 | 作用 |
  | ping | Ping() | 测试连通 |
  | wake | Wake() | 模拟按电源键 |
  | find | Find() | 查找页面元素 |
  | setText | SetText() | 设置文本内容 |
  | getText | GetText() | 获取文本内容 |
  | cick | Click() | 点击元素 |
  | clearText | ClearText() | 清空元素内容 |
  | swipe | Swipe() | 滑动 |
  | getWindowSize | GetWindowSize() | 获取手机长宽 |
  | getProperties | GetProperties() | 获取手机属性 |
  | getSource | GetSource() | 获取当前画面的dump的xml文件 |
