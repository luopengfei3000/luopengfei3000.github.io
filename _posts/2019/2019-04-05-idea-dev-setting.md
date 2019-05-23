---
layout: post
title: IntelliJ IDEA开发设置
no-post-nav: true
category: idea
tags: [idea]
keywords: idea
excerpt : IntelliJ IDEA开发设置
---

## IntelliJ IDEA开发设置

### 1、IntelliJ IDEA设置谷歌浏览器打开

`Ctrl+Alt+s ---> tools ---> web browsers`  进入到浏览器的设置项中

我们可以看到浏览器默认采用的是default(系统默认的)，我们点击下拉菜单，选择定制路径，自己选择需要的浏览器

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/01.png)

浏览器地址可以从桌面浏览器图标中获取：

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/02.png)

### 2、idea 控制台行数限制

在本地进行测试时，会出现报错太多idea控制台被限制打印出来的日志被清楚的现象；

idea改变控制台打印log限制的方法：

#### (1)、点击 `File -> Settings ->editor -> General -> Console` 选中如下图 idea 默认的输出缓存区大小只有1024KB，超过大小限制的就会被清除；

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/03.png)

#### (2)、在idea7之后的版本中就已经取消了对控制台的行数限制选项，只能通过更改配置文件进行更改

在安装目录/bin中找到`idea.properties`文件，更改idea.cycle.buffer.size项值为disabled，保存，重启idea

`idea.cycle.buffer.size=1024` 修改为 `idea.cycle.buffer.size=disabled`

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/04.png)

### 3、IDEA 设置背景颜色为豆沙绿

1. 快捷键 `ctrl+alt+s` 打开setting 选择 `Editor——Color Scheme——General`
2. 右边选择 `Text——Default text`
3. 点击Background 设置颜色：R:199,  G: 237,  B:204

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/05.png)

### 4、IDEA 设置自动清除无效 import

快捷键 `ctrl+alt+s` 打开setting

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/06.png)

- `Add unambiguous imports on the fly`：快速添加明确的导入。

- `Optimize imports on the fly`：快速优化导入，优化的意思即自动帮助删除无用的导入。

如图勾选，自动清除无效import。

### 5、IDEA 设置打开默认项目配置

快捷键 `ctrl+alt+s` 打开setting

1. 左侧树形菜单，选择 `Appearance & Behavior>System Settings` 进入系统设置页面。

2. 在Startup/Shutdown栏目处，去掉默认的勾选 `Reopen last project on startup`（在启动的时候打开上次的项目），去掉勾选之后，点击确认。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/07.png)

3. 关闭idea软件后，再次打开idea软件，这个时候没有直接进入项目工程，而是进入了欢迎页面，在欢迎页面左侧列表是历史打开的项目工程，可以选择一个打开，右侧可以创建新的工程或导入新的工程等。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/08.png)

4. 在刚刚关闭idea软件的时候，你可能会注意到一个提示框，是确认关闭的提示窗口，防止误操作关闭了当前的工作空间。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/09.png)

5. 可能有时不经意勾选了 `Do not ask me again`。结果每次关闭都不会有确认提示了。一不小心就把当前的窗口误操作关闭了。这个开关就在上面设置的那个取消打开默认工程的配置的下方。将 `Confirm application exit` 选项再次勾选上，关闭确认提示就再次开启了。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-05-idea-dev-setting/10.png)



