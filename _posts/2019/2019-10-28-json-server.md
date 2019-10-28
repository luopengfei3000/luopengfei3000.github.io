---
layout: post
title: json-server 详解
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : json-server 详解
---

## json-server 详解

json-server 是一个 Node 模块，运行 Express 服务器，你可以指定一个 json 文件作为 api 的数据源。

json-server可以直接把一个json文件托管成一个具备全RESTful风格的API,并支持跨域、jsonp、路由订制、数据快照保存等功能的 web 服务器。

#### 安装全局json-server

```
npm install -g json-server
```

- 查看是否安装成功

```
json-server -h
```

#### 当前项目中安装json-server

`--save` 目的是把当前安装的json-server存到package.json文件中

```
npm install json-server --save
```

注：安装过程中package name不能为json-server,否则报错！

#### 启动 json-server

1、 修改package.json文件中scripts位置代码

```
 "scripts": {
    "json:server": "json-server --watch db.json"
  }
```

也可以使用如下命令（修改端口号，默认为3000）

```
"json:server": "json-server --watch --port 53000 db.json"
```

2、 创建db.json并写入json数据

``` 
{
    "user": [
        {
            "name": "张三",
            "phone": "15298741111",
            "id": 1,
            "age": 11,
            "companyId": 1
        },
        {
            "name": "李四",
            "phone": "15298742222",
            "id": 2,
            "age": 22,
            "companyId": 2
        },
        {
            "name": "王五",
            "phone": "15298743333",
            "id": 2,
            "age": 33,
            "companyId": 3
        },
        {
            "name": "刘六",
            "phone": "15298744444",
            "id": 4,
            "age": 44,
            "companyId": 3
        }
    ],
    "companies": [
        {
            "id": 1,
            "name": "Apple",
            "description": "Apple is good"
        },
        {
            "id": 2,
            "name": "alibaba",
            "description": "Alibaba is good"
        },
        {
            "id": 3,
            "name": "JD",
            "description": "JD is good"
        }
    ]
}
```

3、 启动项目(cmd中指定项目位置)

```
npm run json:server
```