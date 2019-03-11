---
layout: post
title: windows下MongoDB的安装及配置
no-post-nav: true
category: mongodb
tags: [mongodb]
keywords: mongodb
---
这篇文件介绍windows下MongoDB的安装及配置。
#### 一、先登录Mongodb官网https://www.mongodb.com/download-center#community下载安装包(32、64位)
![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-11-mongodb-install/01.png)
#### 二、安装MongoDB
![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-11-mongodb-install/02.png)

![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-11-mongodb-install/03.png)
 
![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-11-mongodb-install/04.png)

然后不断“下一步”，安装至结束。<br/>
安装比较容易。难点在启动Mongodb的服务以及将MongoDB设置成Windows服务，加配置文件在windows的“服务”中找到。

#### 三、先创建数据库文件的存放位置

在MongoDB下创建data，在data下再创建db：D:\software\MongoDB\data\db <br/>
因为启动mongodb服务之前需要必须创建数据库文件的存放文件夹，否则命令不会自动创建，而且不能启动成功。

#### 四、启动MongoDB服务
1.打开cmd命令行

2.进入D:\software\MongoDB\bin目录（注意：先输入d:进入d盘，然后输入cd D:\software\MongoDB\bin）

3.输入如下的命令启动mongodb服务：mongod --dbpath D:\software\MongoDB\data\db

![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-11-mongodb-install/05.png)

即是在第三步创建的数据库存放文件路径下启动。<br/>
4.在浏览器输入http://localhost:27017 （27017是mongodb的端口号）查看，若显示：

![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-11-mongodb-install/06.png)

则表示，连接成功。如果不成功，可以查看端口是否被占用。<br/>
但是在本地windows“服务”中，是没有配置上mongodb 服务的，可以打开“服务”看下

#### 五、配置本地windows mongodb 服务
这样可设置为 开机自启动，可直接手动启动关闭，可通过命令行net start MongoDB 启动。该配置会大大方便。<br/>

1.先在data文件下创建一个新文件夹log（用来存放日志文件）

2.在Mongodb新建配置文件mongo.config

3.用记事本打开mongo.config，并输入：<br/>
dbpath=D:\software\MongoDB\data\db <br/>
logpath=D:\software\MongoDB\data\log\mongo.log

4.用管理员身份打开cmd，配置windows服务 <br/>
cmd先跳转到 D:\software\MongoDB\bin目录下。<br/>
输入：mongod --config "D:\software\Mongodb\mongo.config" --install --serviceName "MongoDB"<br/>
即根据刚创建的mongo.config配置文件安装服务，名称为MongoDB。

![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-11-mongodb-install/07.png)

完成后，再次查看本地的服务。

![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-11-mongodb-install/08.png)

