---
layout: post
title: IntelliJ IDEA开发设置
no-post-nav: true
category: idea
tags: [idea]
keywords: idea
excerpt : IntelliJ IDEA开发设置
---

### IntelliJ IDEA开发设置

#### 1、IntelliJ IDEA设置谷歌浏览器打开

Ctrl+Alt+s ---> tools ---> web browsers，进入到浏览器的设置项中

我们可以看到浏览器默认采用的是default(系统默认的)，我们点击下拉菜单，选择定制路径，自己选择需要的浏览器

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/01.png)

浏览器地址可以从桌面浏览器图标中获取：

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/02.png)

#### 2、idea 控制台行数限制

在本地进行测试时，会出现报错太多idea控制台被限制打印出来的日志被清楚的现象；

idea改变控制台打印log限制的方法：

##### 点击 File ->Settings ->editor - >General->Console 选中如下图 idea默认的输出缓存区大小只有1024KB，超过大小限制的就会被清除；

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/03.png)

##### 在idea7之后的版本中就已经取消了对控制台的行数限制选项，只能通过更改配置文件进行更改

在安装目录/bin中找到idea.properties文件，更改idea.cycle.buffer.size项值为disabled，保存，重启idea

idea.cycle.buffer.size=1024修改为：

idea.cycle.buffer.size=disabled

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/04.png)

#### 3、idea设置背景颜色为豆沙绿

1. 打开idea Settings 选择 Editor——Color Scheme——General
2. 右边选择Text——Default text
3. 点击Background 设置颜色：R:199,  G: 237,  B:204

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/05.png)

#### 4、IDEA 设置自动清除无效 import

快捷键:ctrl+alt+s 打开setting

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/06.png)

- Add unambiguous imports on the fly：快速添加明确的导入。

- Optimize imports on the fly：快速优化导入，优化的意思即自动帮助删除无用的导入。

如图勾选，自动清除无效import。