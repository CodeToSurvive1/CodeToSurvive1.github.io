---
layout: "post"
title: "springside4环境搭建 "
date: "2016-11-02 08:18"
category: "j2ee"
tags: [springside4,j2ee]
---



#### springside4环境搭建    

```xml

官方地址[https://github.com/springside/springside4](https://github.com/springside/springside4)

由于官方的https://github.com/springside/springside4目前的主分支是master，直接下载会报错，因此需要下载4.0分支，但是启动后查看quickstart仍然会报错，这里直接fork一个版本，在官方基础上进行改造。因此可以直接下载本人的分支,[hhttps://github.com/CodeToSurvive1/springside4.git](https://github.com/CodeToSurvive1/springside4.git)，注意，本人已经将主分支切换到修复错误后的分支，可以直接clone即可  	 	

```

下面会从官方中直接fork进行操作，如果直接使用本人的	[https://github.com/CodeToSurvive1/springside4.git](https://github.com/CodeToSurvive1/springside4.git)分支，请跳过下面(一，fork改造	)，直接进入（二，环境搭建）		 	
------

##### 一，fork改造	

1.首先从https://github.com/springside/springside4直接fork 	

![fork](../assets/2016/11/2016-11-12_10-22-26.png)


2.进入到自己fork的分支上，复制地址 		

![clone](../assets/2016/11/2016-11-12_10-24-10.png)


3.通过git clone 直接下载,注意，这样加了参数-b意思为下载分支branch，默认不加参数是下载的主分支 		 


```xml

git clone -b dev_4.0 https://github.com/CodeToSurvive1/springside4.git
Cloning into 'springside4'...
remote: Counting objects: 24321, done.
remote: Total 24321 (delta 0), reused 0 (delta 0), pack-reused 24321
Receiving objects: 100% (24321/24321), 3.82 MiB | 520.00 KiB/s, done.
Resolving deltas: 100% (8658/8658), done.
Checking connectivity... done.

```

------

##### 二，环境搭建	

1.下载后，直接进入到springside4中,运行启动命令即可  				


```xml

pwd
/Users/mac/software/github/springside4
mac:springside4 mac$ ./quick-start.sh 

```

2.经过漫长的maven下载编译过程过后,会在后台启动服务，这时候直接访问url即可(如果maven下载缓慢，可以直接下载本人共享的maven的repository[](https://pan.baidu.com/s/1c2mktoO)),不过百度云盘也够慢的，自己选择吧		 	

[http://localhost:8081/showcase/](http://localhost:8081/showcase/)  案例集合  	

![案例集合](../assets/2016/11/2016-11-12_10-54-30.png)

[http://localhost:8080/quickstart/](http://localhost:8080/quickstart/)  快速演示  	

![快速演示](../assets/2016/11/2016-11-12_10-55-11.png)

用admin，admin登录报错，是由于代码问题，如果直接fork的我的版本，是没有问题的,会直接看到下面的步骤6的成功页面		 	

![错误](../assets/2016/11/2016-11-12_10-56-04.png)	

3.直接用idea打开后,看到下面目录结构 		

![idea](../assets/2016/11/2016-11-12_11-00-41.png)


4.打开路径/Users/mac/software/github/springside4/examples/quickstart/src/main/webapp/WEB-INF/tags/pagination.tag文件，直接将下面内容替换掉 		

![错误内容](../assets/2016/11/2016-11-12_11-02-56.png)	

直接修改为 	

![修改后](../assets/2016/11/2016-11-12_11-03-45.png)

5.再次启动命令，进入页面查看	

首先干掉上次启动服务后的两个后台进程，否则报端口冲突 		
```xml
ps -ef | grep jetty
  501  3466     1   0 10:51上午 ttys005    1:00.60 /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home/bin/java -classpath /Users/mac/software/apache-maven-3.3.9/boot/plexus-classworlds-2.5.2.jar -Dclassworlds.conf=/Users/mac/software/apache-maven-3.3.9/bin/m2.conf -Dmaven.home=/Users/mac/software/apache-maven-3.3.9 -Dmaven.multiModuleProjectDirectory=/Users/mac/software/github/springside4/examples/quickstart org.codehaus.plexus.classworlds.launcher.Launcher clean jetty:run
  501  3467     1   0 10:51上午 ttys005    1:29.03 /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home/bin/java -classpath /Users/mac/software/apache-maven-3.3.9/boot/plexus-classworlds-2.5.2.jar -Dclassworlds.conf=/Users/mac/software/apache-maven-3.3.9/bin/m2.conf -Dmaven.home=/Users/mac/software/apache-maven-3.3.9 -Dmaven.multiModuleProjectDirectory=/Users/mac/software/github/springside4/examples/showcase org.codehaus.plexus.classworlds.launcher.Launcher clean jetty:run -Djetty.port=8081
  501  4162  2854   0 11:04上午 ttys005    0:00.00 grep jetty
mac:springside4 mac$ kill -9 3466
mac:springside4 mac$ kill -9 3467

```

```xml

./quick-start.sh 

[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 14.296 s
[INFO] Finished at: 2016-11-12T11:06:39+08:00
[INFO] Final Memory: 32M/277M
[INFO] ------------------------------------------------------------------------
[Step 3] Start all example projects.
[INFO] Please wait a moment then access below demo sites:
[INFO] http://localhost:8080/quickstart
[INFO] http://localhost:8081/showcase

```

6.继续查看刚才报错的页面http://localhost:8080/quickstart,登录后	

![成功页面](../assets/2016/11/2016-11-12_11-07-32.png)	

------

#### 三，使用springside4创建自己的maven工程,并配置为mysql数据库		  		

1.直接执行命令generate-project.sh 		 	

```xml

 pwd
/Users/mac/software/github/springside4
mac:springside4 mac$ ./generate-project.sh 

```

2.通过命令行输入maven相关配置后  		

```xml

[INFO] Generating project in ./generated-projects
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building Maven Stub Project (No POM) 1
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] >>> maven-archetype-plugin:2.4:generate (default-cli) > generate-sources @ standalone-pom >>>
[INFO] 
[INFO] <<< maven-archetype-plugin:2.4:generate (default-cli) < generate-sources @ standalone-pom <<<
[INFO] 
[INFO] --- maven-archetype-plugin:2.4:generate (default-cli) @ standalone-pom ---
[INFO] Generating project in Interactive mode
[WARNING] Archetype not found in any catalog. Falling back to central repository (http://repo.maven.apache.org/maven2).
[WARNING] Use -DarchetypeRepository=<your repository> if archetype's repository is elsewhere.
Downloading: http://repo.maven.apache.org/maven2/io/springside/examples/quickstart-archetype/4.3.0-SNAPSHOT/maven-metadata.xml
Define value for property 'groupId': : com.hitwh
Define value for property 'artifactId': : hadoop_project
Define value for property 'version':  1.0-SNAPSHOT: : 1.0
Define value for property 'package':  com.hitwh: : hadoop
Define value for property 'tablePrefix': : hadoop_
Confirm properties configuration:
groupId: com.hitwh
artifactId: hadoop_project
version: 1.0
package: hadoop
tablePrefix: hadoop_
 Y: : Y
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: quickstart-archetype:4.3.0-SNAPSHOT
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.hitwh
[INFO] Parameter: artifactId, Value: hadoop_project
[INFO] Parameter: version, Value: 1.0
[INFO] Parameter: package, Value: hadoop
[INFO] Parameter: packageInPathFormat, Value: hadoop
[INFO] Parameter: tablePrefix, Value: hadoop_
[INFO] Parameter: package, Value: hadoop
[INFO] Parameter: version, Value: 1.0
[INFO] Parameter: groupId, Value: com.hitwh
[INFO] Parameter: artifactId, Value: hadoop_project
[INFO] project created from Archetype in dir: /Users/mac/software/github/springside4/generated-projects/hadoop_project
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 01:32 min
[INFO] Finished at: 2016-11-12T11:13:19+08:00
[INFO] Final Memory: 15M/205M
[INFO] ------------------------------------------------------------------------

```

3.这个时候会在generated-projects文件夹下出现刚才的项目 			

![新建项目](../assets/2016/11/2016-11-12_11-19-29.png)	

4.修改pom文件 		

解注掉mysql驱动包配置		

```xml
<jdbc.driver.groupId>mysql</jdbc.driver.groupId>
<jdbc.driver.artifactId>mysql-connector-java</jdbc.driver.artifactId>
<jdbc.driver.version>5.1.22</jdbc.driver.version>

```

并将最下面的配置文件		

```xml

<!-- 刷新开发环境数据库 -->
		<profile>
			<id>refresh-db</id>
			<build>
				<plugins>
					<plugin>
						<groupId>org.apache.maven.plugins</groupId>
						<artifactId>maven-antrun-plugin</artifactId>
						<configuration>
							<target>
								<property file="src/main/resources/application.development.properties" />
								<property file="src/main/resources/application.properties" />
								<sql driver="${jdbc.driver}" url="${jdbc.url}" userid="${jdbc.username}" password="${jdbc.password}" onerror="continue" encoding="${project.build.sourceEncoding}">
									<classpath refid="maven.test.classpath" />
									<transaction src="src/main/resources/sql/h2/schema.sql" />
									<transaction src="src/test/resources/data/h2/import-data.sql" />
								</sql>
							</target>
						</configuration>
					</plugin>
				</plugins>
			</build>
		</profile>

```


修改为		

```xml

<!-- 刷新开发环境数据库 -->
		<profile>
			<id>refresh-db</id>
			<build>
				<plugins>
					<plugin>
						<groupId>org.apache.maven.plugins</groupId>
						<artifactId>maven-antrun-plugin</artifactId>
						<configuration>
							<target>
								<property file="src/main/resources/application.development.properties" />
								<property file="src/main/resources/application.properties" />
								<sql driver="${jdbc.driver}" url="${jdbc.url}" userid="${jdbc.username}" password="${jdbc.password}" onerror="continue" encoding="${project.build.sourceEncoding}">
									<classpath refid="maven.test.classpath" />
									<transaction src="src/main/resources/sql/mysql/schema.sql" />
									<transaction src="src/test/resources/data/h2/import-data.sql" />
								</sql>
							</target>
						</configuration>
					</plugin>
				</plugins>
			</build>
		</profile>

```

5.修改/Users/mac/software/github/springside4/generated-projects/hadoop_project/src/main/resources/application.properties中的配置，改为mysql配置		

```xml

#h2 database settings
#jdbc.driver=org.h2.Driver
#jdbc.url=jdbc:h2:file:~/.h2/hadoop_project;AUTO_SERVER=TRUE;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
#jdbc.username=sa
#jdbc.password=

#log4jdbc driver
#jdbc.driver=net.sf.log4jdbc.DriverSpy
#jdbc.url=jdbc:log4jdbc:h2:file:~/.h2/hadoop_project;AUTO_SERVER=TRUE;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE

#oracle database settings
#jdbc.driver=oracle.jdbc.driver.OracleDriver
#jdbc.url=jdbc:oracle:thin:@127.0.0.1:1521:XE
#jdbc.username=hadoop_project
#jdbc.password=hadoop_project

#mysql database setting
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/hadoop_project?useUnicode=true&characterEncoding=utf-8
jdbc.username=root
jdbc.password=123456

#connection pool settings
jdbc.pool.maxIdle=10
jdbc.pool.maxActive=50


```

6.修改路径/Users/mac/software/github/springside4/generated-projects/hadoop_project/src/main/resources/sql/mysql/schema.sql中的脚本，否则报错Invalid default value for 'register_date'

```sql

drop table if exists hadoop_task;
drop table if exists hadoop_user;

create table hadoop_task (
	id bigint auto_increment,
	title varchar(128) not null,
	description varchar(255),
	user_id bigint not null,
    primary key (id)
) engine=InnoDB;

create table hadoop_user (
	id bigint auto_increment,
	login_name varchar(64) not null unique,
	name varchar(64) not null,
	password varchar(255) not null,
	salt varchar(64) not null,
	roles varchar(255) not null,
	register_date timestamp not null ,
	primary key (id)
) engine=InnoDB;

```



7.在mysql数据库中建立hadoop_project数据库 		

8.执行切换到目录下执行建表语句 		

```xml

mac:bin mac$ pwd
/Users/mac/software/github/springside4/generated-projects/hadoop_project/bin
mac:bin mac$ ./refresh-db.sh 
[INFO] Re-create the schema and provision the sample data.
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building Springside :: Example :: QuickStart 1.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-antrun-plugin:1.7:run (default-cli) @ hadoop_project ---
[INFO] Executing tasks

main:
      [sql] Executing resource: /Users/mac/software/github/springside4/generated-projects/hadoop_project/src/main/resources/sql/mysql/schema.sql
      [sql] Executing resource: /Users/mac/software/github/springside4/generated-projects/hadoop_project/src/test/resources/data/h2/import-data.sql
      [sql] 11 of 11 SQL statements executed successfully
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 2.270 s
[INFO] Finished at: 2016-11-12T11:37:35+08:00
[INFO] Final Memory: 12M/227M
[INFO] ------------------------------------------------------------------------
mac:bin mac$ 


```

9.同样需要修改/Users/mac/software/github/springside4/generated-projects/hadoop_project/src/main/webapp/WEB-INF/tags/pagination.tag文件中的两处错误 	
修改后为 		

![修改后](../assets/2016/11/2016-11-12_11-39-01.png)	

10.单独打开新建的hadoop_project项目  		

重新导入maven相关包，

![导入maven包](../assets/2016/11/2016-11-12_11-42-30.png)


11.新建tomcat服务器，并进行相关配置 	

![tomcat1](../assets/2016/11/2016-11-12_11-45-54.png)

![tomcat1](../assets/2016/11/2016-11-12_11-46-50.png)

12.启动tomcat，不要忘记先干掉之前的jetty进程，访问8080端口	

http://localhost:8080/login

![tomcat成功](../assets/2016/11/2016-11-12_11-49-56.png)


------

生成的hadoop_project地址可以通过[https://github.com/CodeToSurvive1/hadoop_project](https://github.com/CodeToSurvive1/hadoop_project)下载








