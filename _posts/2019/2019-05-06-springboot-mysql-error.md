---
layout: post
title: springboot启动报错 'ÖÐ¹ú±ê×¼Ê±¼ä'
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more than one time 报错
---

### 1.前言
  今天在用SpringBoot2.0+MyBatis+MySQL搭建项目开发环境的时候启动项目发现报了一个很奇怪的错，报错内容如下：
 
```
java.sql.SQLException: The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more than one time
	zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a
	more specifc time zone value if you want to utilize time zone support.
    at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:127)
    at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:95)
    at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:87)
    at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:61)
    at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:71)
    at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:76)
    at com.mysql.cj.jdbc.ConnectionImpl.createNewIO(ConnectionImpl.java:862)
    at com.mysql.cj.jdbc.ConnectionImpl.<init>(ConnectionImpl.java:444)
    at com.mysql.cj.jdbc.ConnectionImpl.getInstance(ConnectionImpl.java:230)
    at com.mysql.cj.jdbc.NonRegisteringDriver.connect(NonRegisteringDriver.java:226)
    at com.alibaba.druid.pool.DruidAbstractDataSource.createPhysicalConnection(DruidAbstractDataSource.java:1558)
    at com.alibaba.druid.pool.DruidAbstractDataSource.createPhysicalConnection(DruidAbstractDataSource.java:1623)
    at com.alibaba.druid.pool.DruidDataSource$CreateConnectionThread.run(DruidDataSource.java:2468)
``` 

### 2.解决办法

查看Mysql的默认系统值：

![](https://luopengfei3000.github.io/assets/images/2019/springboot/2019-05-06-springboot-mysql-error/01.png)

修改如下：

修改MySQL的配置文件，MySQL配置文件是my.ini文件在你的安装目录下去找，我的是在C:\Program Files (x86)\MySQL\MySQL Server 5.5\my.ini

找到这个文件之后用Notepad++打开它，搜索[mysqld]节点在节点下面加上下面这句话

```
default-time-zone='+08:00'
```

![](https://luopengfei3000.github.io/assets/images/2019/springboot/2019-05-06-springboot-mysql-error/02.png)

然后记得重启MySQL的服务，打开cmd窗口登录MySQL执行
```
set global time_zone='+8:00';
show variables like '%time_zone%';
```

![](https://luopengfei3000.github.io/assets/images/2019/springboot/2019-05-06-springboot-mysql-error/03.png)