---
layout: "post"
title: "springside4自定义项目配置 "
date: "2016-11-13 15:18"
category: "j2ee"
tags: [springside4,j2ee]
---



#### springside4自定义项目配置		


从上一篇文章[springside4环境搭建](https://codetosurvive1.github.io/posts/springside4-environment.html)中可以看出，通过./generate-project.sh生成项目后，还需要手工改page的代码，如果想配置mysql数据库，还需要自己改造pom文件及其相关的配置文件，很是麻烦，那么如何直接改springside4的模版引擎呢。这里springside4是使用了maven-archetype来作为模版引擎来生成代码的。


1.进入到support目录下会看到maven的archetype，如下图所示 		

![archetype](../assets/2016/11/2016-11-13_11-11-52.png)	


2.首先修改pom文件 		

解注掉mysql的驱动包程序 			

```xml

<jdbc.driver.groupId>mysql</jdbc.driver.groupId>
<jdbc.driver.artifactId>mysql-connector-java</jdbc.driver.artifactId>
<jdbc.driver.version>5.1.22</jdbc.driver.version>

```


修改mysql初始化数据的脚本目录		

由 		

```xml
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


3.修改翻页模版代码，进入到/Users/mac/software/github/springside4/support/maven-archetype/src/main/resources/archetype-resources/src/main/webapp/WEB-INF/tags/pagination.tag文件进行修改   		

修改为 		


```xml

<%@tag pageEncoding="UTF-8"%>
<%@ attribute name="page" type="org.springframework.data.domain.Page" required="true"%>
<%@ attribute name="paginationSize" type="java.lang.Integer" required="true"%>

<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>

<%
int current =  page.getNumber() + 1;
int begin = Math.max(1, current - paginationSize/2);
int end = Math.min(begin + (paginationSize - 1), page.getTotalPages());

request.setAttribute("current", current);
request.setAttribute("begin", begin);
request.setAttribute("end", end);
%>

<div class="pagination">
	<ul>
		 <% if (page.hasPrevious()){%>
               	<li><a href="?page=1&sortType=${sortType}&${searchParams}">&lt;&lt;</a></li>
                <li><a href="?page=${current-1}&sortType=${sortType}&${searchParams}">&lt;</a></li>
         <%}else{%>
                <li class="disabled"><a href="#">&lt;&lt;</a></li>
                <li class="disabled"><a href="#">&lt;</a></li>
         <%} %>
 
		<c:forEach var="i" begin="${begin}" end="${end}">
            <c:choose>
                <c:when test="${i == current}">
                    <li class="active"><a href="?page=${i}&sortType=${sortType}&${searchParams}">${i}</a></li>
                </c:when>
                <c:otherwise>
                    <li><a href="?page=${i}&sortType=${sortType}&${searchParams}">${i}</a></li>
                </c:otherwise>
            </c:choose>
        </c:forEach>
	  
	  	 <% if (page.hasNext()){%>
               	<li><a href="?page=${current+1}&sortType=${sortType}&${searchParams}">&gt;</a></li>
                <li><a href="?page=${page.totalPages}&sortType=${sortType}&${searchParams}">&gt;&gt;</a></li>
         <%}else{%>
                <li class="disabled"><a href="#">&gt;</a></li>
                <li class="disabled"><a href="#">&gt;&gt;</a></li>
         <%} %>

	</ul>
</div>



```

4.修改/Users/mac/software/github/springside4/support/maven-archetype/src/main/resources/archetype-resources/src/main/resources/application.properties文件 	

修改为 		


```xml

#set( $symbol_pound = '#' )
#set( $symbol_dollar = '$' )
#set( $symbol_escape = '\' )
${symbol_pound}h2 database settings
${symbol_pound}jdbc.driver=org.h2.Driver
${symbol_pound}jdbc.url=jdbc:h2:file:~/.h2/${artifactId};AUTO_SERVER=TRUE;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
${symbol_pound}jdbc.username=sa
${symbol_pound}jdbc.password=

${symbol_pound}log4jdbc driver
${symbol_pound}jdbc.driver=net.sf.log4jdbc.DriverSpy
${symbol_pound}jdbc.url=jdbc:log4jdbc:h2:file:~/.h2/${artifactId};AUTO_SERVER=TRUE;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE

${symbol_pound}oracle database settings
${symbol_pound}jdbc.driver=oracle.jdbc.driver.OracleDriver
${symbol_pound}jdbc.url=jdbc:oracle:thin:@127.0.0.1:1521:XE
${symbol_pound}jdbc.username=${artifactId}
${symbol_pound}jdbc.password=${artifactId}

${symbol_pound}mysql database setting
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/${artifactId}?useUnicode=true&characterEncoding=utf-8
jdbc.username=root
jdbc.password=123456

${symbol_pound}connection pool settings
jdbc.pool.maxIdle=10
jdbc.pool.maxActive=50

```

5.修改mysql的脚本，否则报错/Users/mac/software/github/springside4/support/maven-archetype/src/main/resources/archetype-resources/src/main/resources/sql/mysql/schema.sql,将register_date的default 0 删除					
修改后		


```xml

#set( $symbol_pound = '#' )
#set( $symbol_dollar = '$' )
#set( $symbol_escape = '\' )
drop table if exists ${tablePrefix}task;
drop table if exists ${tablePrefix}user;

create table ${tablePrefix}task (
	id bigint auto_increment,
	title varchar(128) not null,
	description varchar(255),
	user_id bigint not null,
    primary key (id)
) engine=InnoDB;

create table ${tablePrefix}user (
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

6.进入到/Users/mac/software/github/springside4/support/maven-archetype目录下重新安装		

./install.sh 		

```xml

./install.sh 

[INFO] Install archetype to local repository.
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building SpringSide :: Archetype :: QuickStart 4.3.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ quickstart-archetype ---
[INFO] Deleting /Users/mac/software/github/springside4/support/maven-archetype/target
[INFO] 
[INFO] --- maven-resources-plugin:3.0.1:resources (default-resources) @ quickstart-archetype ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] Copying 87 resources
[INFO] 
[INFO] --- maven-resources-plugin:3.0.1:testResources (default-testResources) @ quickstart-archetype ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /Users/mac/software/github/springside4/support/maven-archetype/src/test/resources
[INFO] 
[INFO] --- maven-archetype-plugin:2.2:jar (default-jar) @ quickstart-archetype ---
[INFO] Building archetype jar: /Users/mac/software/github/springside4/support/maven-archetype/target/quickstart-archetype-4.3.0-SNAPSHOT
[INFO] 
[INFO] --- maven-archetype-plugin:2.2:integration-test (default-integration-test) @ quickstart-archetype ---
[WARNING] No Archetype IT projects: root 'projects' directory not found.
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ quickstart-archetype ---
[INFO] Installing /Users/mac/software/github/springside4/support/maven-archetype/target/quickstart-archetype-4.3.0-SNAPSHOT.jar to /Users/mac/software/repository/io/springside/examples/quickstart-archetype/4.3.0-SNAPSHOT/quickstart-archetype-4.3.0-SNAPSHOT.jar
[INFO] Installing /Users/mac/software/github/springside4/support/maven-archetype/pom.xml to /Users/mac/software/repository/io/springside/examples/quickstart-archetype/4.3.0-SNAPSHOT/quickstart-archetype-4.3.0-SNAPSHOT.pom
[INFO] 
[INFO] --- maven-archetype-plugin:2.2:update-local-catalog (default-update-local-catalog) @ quickstart-archetype ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 2.318 s
[INFO] Finished at: 2016-11-13T11:23:03+08:00
[INFO] Final Memory: 13M/155M
[INFO] ------------------------------------------------------------------------

```

7.重新切换回/Users/mac/software/github/springside4目录，并重新新建项目 		


```xml

./generate-project.sh 
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
Define value for property 'artifactId': : dataapi
Define value for property 'version':  1.0-SNAPSHOT: : 1.0
Define value for property 'package':  com.hitwh: : dataapi
Define value for property 'tablePrefix': : hadoop_
Confirm properties configuration:
groupId: com.hitwh
artifactId: dataapi
version: 1.0
package: dataapi
tablePrefix: hadoop_
 Y: : Y
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: quickstart-archetype:4.3.0-SNAPSHOT
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.hitwh
[INFO] Parameter: artifactId, Value: dataapi
[INFO] Parameter: version, Value: 1.0
[INFO] Parameter: package, Value: dataapi
[INFO] Parameter: packageInPathFormat, Value: dataapi
[INFO] Parameter: tablePrefix, Value: hadoop_
[INFO] Parameter: package, Value: dataapi
[INFO] Parameter: version, Value: 1.0
[INFO] Parameter: groupId, Value: com.hitwh
[INFO] Parameter: artifactId, Value: dataapi
[INFO] project created from Archetype in dir: /Users/mac/software/github/springside4/generated-projects/dataapi
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 39.115 s
[INFO] Finished at: 2016-11-13T11:24:54+08:00
[INFO] Final Memory: 14M/177M
[INFO] ------------------------------------------------------------------------

```

8.进入到生成的dataapi项目下,并给bin目录下的脚本添加可执行权限  	  	

```xml

cd /Users/mac/software/github/springside4/generated-projects/dataapi

chmod a+x ./bin/*

然后在mysql数据库中添加项目名称的数据库,否则报错数据库找不到  	

新建数据库后执行bin目录下的		

cd bin

./refresh-db.sh 

```



9.直接用idea打开dataapi工程，启动tomcat，查看首页  		 		

![tomcat1](../assets/2016/11/2016-11-13_15-43-47.png)

![tomcat2](../assets/2016/11/2016-11-13_15-44-44.png)

![首页](../assets/2016/11/2016-11-13_15-45-07.png)


------



当然，上面修改的模版引擎已经提交到[https://github.com/CodeToSurvive1/springside4](https://github.com/CodeToSurvive1/springside4)的默认分支dev_4.0上，直接fork就不用修改了		



