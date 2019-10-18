---
layout: post
title: IntelliJ IDEA 中UTF-8乱码问题
no-post-nav: true
category: idea
tags: [idea]
keywords: idea
excerpt : IntelliJ IDEA 中UTF-8乱码问题
---

## IntelliJ IDEA 中UTF-8乱码问题

### 第一个:File->Settings->Editor->File Encodings

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-21-idea-encoded-utf8/01.png)

### 第二个:File->Settings->Build,Execution,Deployment -> Compiler -> Java Compiler

设置 Additional command line parameters选项为 -encoding utf-8

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-21-idea-encoded-utf8/02.png)

### 第三个:找到idea安装目录bin目录下如下图所示两个文件，用编辑器打开，在文件末尾添加 -Dfile.encoding=UTF-8

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-21-idea-encoded-utf8/03.png)






