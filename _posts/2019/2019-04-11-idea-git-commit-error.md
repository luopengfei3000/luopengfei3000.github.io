---
layout: post
title: IntelliJ IDEA使用GIT push时遇到的一些问题
no-post-nav: true
category: idea
tags: [idea]
keywords: idea,git
excerpt : IntelliJ IDEA使用GIT push时遇到的问题
---

## IDEA使用GIT push的时候提示 "push to origin/master was rejected" 解决方案

**push to origin/master was rejected错误解决方案**

idea中，发布项目到`github`中，当时按照流程添加Git，然后push，提示："push to origin/master war rejected"。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-11-idea-git-commit-error/01.png)

解决方案如下：

切换到自己项目所在的目录，打开Terminal窗口(Alt+F12)，执行如下命令：

- git pull

- git pull origin master

- git pull origin master --allow-unrelated-histories

命令执行完毕后，在idea中重新push自己的项目，成功！！！

## IDEA使用GIT push出现 "fatal: The remote end hung up unexpectedly" 解决方案


**fatal: The remote end hung up unexpectedly错误解决方案**

idea中，发布项目到`github`中，当时按照流程添加Git，然后push，提示："fatal: The remote end hung up unexpectedly"。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-11-idea-git-commit-error/02.png)

解决方案如下：

在项目下的`.git`目录下面修改生成的config文件增加如下：
```
[http]
postBuffer = 524288000
```

在idea中重新push自己的项目，成功！！！




