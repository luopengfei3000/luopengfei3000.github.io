---
layout: post
title: 修改 IntelliJ IDEA 缓存文件位置
no-post-nav: true
category: idea
tags: [idea]
keywords: idea
excerpt : 修改 IntelliJ IDEA 缓存文件位置
---

## 修改 IntelliJ IDEA 缓存文件位置

软件虽然装在D盘，C盘仍然还有一个缓存文件夹

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-20-idea-cache-file-location/01.png)

里面有两个文件夹：
- config 目录是 IntelliJ IDEA 个性化化配置目录，或者说是整个 IDE 设置目录。也是我个人认为最重要的目录，没有之一，安装新版本的 IntelliJ IDEA 会自动扫描硬盘上的旧配置目录，指的就是该目录。这个目录主要记录了：IDE 主要配置功能、自定义的代码模板、自定义的文件模板、自定义的快捷键、Project 的 tasks 记录等等个性化的设置。
- system 目录是 IntelliJ IDEA 系统文件目录，是 IntelliJ IDEA 与开发项目一个桥梁目录，里面主要有：缓存、索引、容器文件输出等等，虽然不是最重要目录，但是也是最不可或缺目录之一。

现在来修改：打开idea的安装路径下的bin文件夹

修改idea.properties的配置

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-20-idea-cache-file-location/01.png)

把注释去掉，使用自定义配置，修改${user.home}为你想要使用的路径

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-20-idea-cache-file-location/01.png)

最后直接把C盘下的.IntelliJIdea2018.1 复制到 D:\Idea2018\ 下，修改如下：

```
idea.config.path=D:/Idea2018/.IntelliJIdea/config
idea.system.path=D:/Idea2018/.IntelliJIdea/system
```

