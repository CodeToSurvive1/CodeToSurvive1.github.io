---
layout: "post"
title: "ofbiz16.11环境搭建"
date: "2017-02-19 08:52"
category: "j2ee"
tags: [ofbiz,javaweb,j2ee]

---


#### ofbiz下载安装  

1.下载    

进入ofbiz官方页面http://ofbiz.apache.org/download.html页面下载最新版本16.11.01		


2.下载gradle   

进入https://gradle.org/install#download页面下载gradle最新版本，这里是gradle-3.2.1-bin.zip		


3.解压ofbiz后，将gradle-3.2.1-bin.zip拷贝到ofbiz目录下的gradle/wrapper/目录中  	

4.修改gradle/wrapper/中的文件gradle-wrapper.properties中的distributionUrl属性，修改后如下所示 		

```properties

#Sat Feb 18 13:07:49 CST 2017
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
#distributionUrl=https\://services.gradle.org/distributions/gradle-2.13-all.zip
distributionUrl=gradle-3.2.1-bin.zip

```

5.执行命令前修改gradle下载镜像地址为阿里云地址，只需要在用户主目录下的gradle相关目录下创建文件，并填写如下内容即可   

这里的主目录为/Users/mac/.gradle，创建文件init.gradle，并填写如下内容  	

```bash

allprojects{
    repositories {
        def ALIYUN_REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public'
        def ALIYUN_JCENTER_URL = 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_REPOSITORY_URL."
                    remove repo
                }
                if (url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_JCENTER_URL."
                    remove repo
                }
            }
        }
        maven {
        	url ALIYUN_REPOSITORY_URL
            url ALIYUN_JCENTER_URL
        }
    }
}


```


6.执行启动命令,执行过程中会下载相关依赖包，并进行启动    	


```bash

gradlew loadDefault ofbiz

```

启动完成后会出现下面提示：	

```bash

 [[[main(Domain:https://localhost)] Request Done- total:0.595,since last([main(Domain:http...):0.595]]
> Building 92% > :ofbiz

```
卡在这里不动的时候可以忽略  	

7.登录页面http://localhost:8080/catalog 后会自动跳转到登录页面https://localhost:8443/catalog/control/main，输入用户名及密码admin/ofbiz 		


#### 修改默认数据库为mysql数据库 		

1.新建ofbiz相关数据库  

```sql

mysql -uroot -p123456

mysql> create database ofbiz;
Query OK, 1 row affected (0.00 sec)

mysql> create database ofbizolap;
Query OK, 1 row affected (0.00 sec)

mysql> create database ofbiztenant;
Query OK, 1 row affected (0.00 sec)

mysql> create database ofbiz_odbc;
Query OK, 1 row affected (0.00 sec)

```

2.修改配置文件apache-ofbiz-16.11.01/framework/entity/config/entityengine.xml  		

修改localmysql，localmysqlolap，localmysqltenant,odbcmysql节点中的jdbc-username,jdbc-password为自己mysql的用户名及密码  	

```xml
<datasource name="localmysql"
        helper-class="org.apache.ofbiz.entity.datasource.GenericHelperDAO"
        field-type-name="mysql"
        check-on-start="true"
        add-missing-on-start="true"
        check-pks-on-start="false"
        use-foreign-keys="true"
        join-style="ansi-no-parenthesis"
        alias-view-columns="false"
        drop-fk-use-foreign-key-keyword="true"
        table-type="InnoDB"
        character-set="latin1"
        collate="latin1_general_cs">
    <read-data reader-name="tenant"/>
    <read-data reader-name="seed"/>
    <read-data reader-name="seed-initial"/>
    <read-data reader-name="demo"/>
    <read-data reader-name="ext"/>
    <read-data reader-name="ext-test"/>
    <read-data reader-name="ext-demo"/>
    <inline-jdbc
            jdbc-driver="com.mysql.jdbc.Driver"
            jdbc-uri="jdbc:mysql://127.0.0.1/ofbiz?autoReconnect=true"
            jdbc-username="root"
            jdbc-password="123456"
            isolation-level="ReadCommitted"
            pool-minsize="2"
            pool-maxsize="250"
            time-between-eviction-runs-millis="600000"/><!-- Please note that at least one person has experienced a problem with this value with MySQL
            and had to set it to -1 in order to avoid this issue.
            For more look at http://markmail.org/thread/5sivpykv7xkl66px and http://commons.apache.org/dbcp/configuration.html-->
    <!-- <jndi-jdbc jndi-server-name="localjndi" jndi-name="java:/MySqlDataSource" isolation-level="Serializable"/> -->
</datasource>
<datasource name="localmysqlolap"
        helper-class="org.apache.ofbiz.entity.datasource.GenericHelperDAO"
        field-type-name="mysql"
        check-on-start="true"
        add-missing-on-start="true"
        check-pks-on-start="false"
        use-foreign-keys="true"
        join-style="ansi-no-parenthesis"
        alias-view-columns="false"
        drop-fk-use-foreign-key-keyword="true"
        table-type="InnoDB"
        character-set="latin1"
        collate="latin1_general_cs">
    <read-data reader-name="tenant"/>
    <read-data reader-name="seed"/>
    <read-data reader-name="seed-initial"/>
    <read-data reader-name="demo"/>
    <read-data reader-name="ext"/>
    <read-data reader-name="ext-test"/>
    <read-data reader-name="ext-demo"/>
    <inline-jdbc
            jdbc-driver="com.mysql.jdbc.Driver"
            jdbc-uri="jdbc:mysql://127.0.0.1/ofbizolap?autoReconnect=true"
            jdbc-username="root"
            jdbc-password="123456"
            isolation-level="ReadCommitted"
            pool-minsize="2"
            pool-maxsize="250"
            time-between-eviction-runs-millis="600000"/><!-- Please note that at least one person has experienced a problem with this value with MySQL
            and had to set it to -1 in order to avoid this issue.
            For more look at http://markmail.org/thread/5sivpykv7xkl66px and http://commons.apache.org/dbcp/configuration.html-->
    <!-- <jndi-jdbc jndi-server-name="localjndi" jndi-name="java:/MySqlDataSource" isolation-level="Serializable"/> -->
</datasource>
<datasource name="localmysqltenant"
        helper-class="org.apache.ofbiz.entity.datasource.GenericHelperDAO"
        field-type-name="mysql"
        check-on-start="true"
        add-missing-on-start="true"
        check-pks-on-start="false"
        use-foreign-keys="true"
        join-style="ansi-no-parenthesis"
        alias-view-columns="false"
        drop-fk-use-foreign-key-keyword="true"
        table-type="InnoDB"
        character-set="latin1"
        collate="latin1_general_cs">
    <read-data reader-name="tenant"/>
    <read-data reader-name="seed"/>
    <read-data reader-name="seed-initial"/>
    <read-data reader-name="demo"/>
    <read-data reader-name="ext"/>
    <read-data reader-name="ext-test"/>
    <read-data reader-name="ext-demo"/>
    <inline-jdbc
            jdbc-driver="com.mysql.jdbc.Driver"
            jdbc-uri="jdbc:mysql://127.0.0.1/ofbiztenant?autoReconnect=true"
            jdbc-username="root"
            jdbc-password="123456"
            isolation-level="ReadCommitted"
            pool-minsize="2"
            pool-maxsize="250"
            time-between-eviction-runs-millis="600000"/><!-- Please note that at least one person has experienced a problem with this value with MySQL
            and had to set it to -1 in order to avoid this issue.
            For more look at http://markmail.org/thread/5sivpykv7xkl66px and http://commons.apache.org/dbcp/configuration.html-->
    <!-- <jndi-jdbc jndi-server-name="localjndi" jndi-name="java:/MySqlDataSource" isolation-level="Serializable"/> -->
</datasource>
<datasource name="odbcmysql"
        helper-class="org.apache.ofbiz.entity.datasource.GenericHelperDAO"
        field-type-name="mysql"
        check-on-start="true"
        add-missing-on-start="true"
        check-pks-on-start="false"
        use-foreign-keys="true"
        join-style="ansi-no-parenthesis"
        alias-view-columns="false"
        drop-fk-use-foreign-key-keyword="true"
        table-type="InnoDB"
        character-set="latin1"
        collate="latin1_general_cs">
    <read-data reader-name="tenant"/>
    <read-data reader-name="seed"/>
    <inline-jdbc
            jdbc-driver="com.mysql.jdbc.Driver"
            jdbc-uri="jdbc:mysql://127.0.0.1/ofbiz_odbc?autoReconnect=true"
            jdbc-username="root"
            jdbc-password="123456"
            isolation-level="ReadCommitted"
            pool-minsize="2"
            pool-maxsize="250"
            time-between-eviction-runs-millis="600000"/>
    <!-- <jndi-jdbc jndi-server-name="localjndi" jndi-name="java:/MySqlDataSource" isolation-level="Serializable"/> -->
</datasource>

```

3.仍然修改上面的配置文件中的开始的代理配置为mysql的配置    

```xml

<delegator name="default" entity-model-reader="main" entity-group-reader="main" entity-eca-reader="main" distributed-cache-clear-enabled="false">
    <group-map group-name="org.apache.ofbiz" datasource-name="localmysql"/>
    <group-map group-name="org.apache.ofbiz.olap" datasource-name="localmysqlolap"/>
    <group-map group-name="org.apache.ofbiz.tenant" datasource-name="localmysqltenant"/>
</delegator>
<delegator name="default-no-eca" entity-model-reader="main" entity-group-reader="main" entity-eca-reader="main" entity-eca-enabled="false" distributed-cache-clear-enabled="false">
    <group-map group-name="org.apache.ofbiz" datasource-name="localmysql"/>
    <group-map group-name="org.apache.ofbiz.olap" datasource-name="localmysqlolap"/>
    <group-map group-name="org.apache.ofbiz.tenant" datasource-name="localmysqltenant"/>
</delegator>

<!-- be sure that your default delegator (or the one you use) uses the same datasource for test. You must run "gradlew loadDefault" before running "gradlew testIntegration" -->
<delegator name="test" entity-model-reader="main" entity-group-reader="main" entity-eca-reader="main">
    <group-map group-name="org.apache.ofbiz" datasource-name="localmysql"/>
    <group-map group-name="org.apache.ofbiz.olap" datasource-name="localmysqlolap"/>
    <group-map group-name="org.apache.ofbiz.tenant" datasource-name="localmysqltenant"/>
</delegator>

```

4.重新执行命令  	

```

./gradlew loadDefault ofbiz

```


