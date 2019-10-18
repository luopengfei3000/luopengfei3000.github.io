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

> Transparent native-to-ascii conversion的意思是：自动转换ASCII编码。<br/>
> 他的工作原理是：在文件中输入文字时他会自动的转换为Unicode编码，然后在idea中发开文件时他会自动转回文字来显示。<br/>
> 这样做是为了防止文件乱码。<br/>
> 这样你的properties文件，一般都不会出现中文乱码！

### 第二个:File->Settings->Build,Execution,Deployment -> Compiler -> Java Compiler

设置 Additional command line parameters选项为 -encoding utf-8

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-21-idea-encoded-utf8/02.png)

### 第三个:找到idea安装目录bin目录下如下图所示两个文件，用编辑器打开，在文件末尾添加 -Dfile.encoding=UTF-8

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-21-idea-encoded-utf8/03.png)






