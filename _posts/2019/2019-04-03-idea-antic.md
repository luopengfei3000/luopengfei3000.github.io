---
layout: post
title: Intellij IDEA小技巧
no-post-nav: true
category: idea
tags: [idea]
keywords: idea
excerpt : Intellij IDEA小技巧
---

### Intellij IDEA小技巧

#### 1、神奇的Inject language
如果你使用IDEA在编写JSON字符串的时候，然后要一个一个\去转义双引号的话，就实在太不应该了，又烦又容易出错。在IDEA可以使用Inject language帮我们自动转义双引号。<br/>
先将焦点定位到双引号里面，使用alt+enter快捷键弹出inject language视图，并选中 Inject language or reference。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-03-idea-antic/01.png)

选择后,切记要直接按下enter回车键，才能弹出inject language列表。在列表中选择 json组件。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-03-idea-antic/02.png)

选择完后。鼠标焦点自动会定位在双引号里面，这个时候你再次使用alt+enter就可以看到

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-03-idea-antic/03.png)

可以看到IDEA确实帮我们自动转义双引号了。如果要退出编辑JSON信息的视图，只需要使用ctrl+F4快捷键即可。

#### 2、使用快捷键移动分割线
假设有下面的场景，某个类的名字在project视图里被挡住了某一部分。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-03-idea-antic/04.png)

你想完整的看到类的名字，该怎么做。一般都是使用鼠标来移动分割线，但是这样子效率太低了。可以使用alt+1把鼠标焦点定位到project视图里，然后直接使用ctrl+shift+左右箭头来移动分割线。

#### 3、ctrl+shift+enter不只是用来行尾加分号的

ctrl+shift+enter其实是表示为您收尾的意思，不只是用来给代码加分号的。比如说：

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-03-idea-antic/05.png)

这段代码，我们还需要为if语句加上大括号才能编译通过，这个时候你直接输入ctrl+shift+enter，IDEA会自动帮你收尾，加上大括号的。

#### 4、去掉导航栏

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-03-idea-antic/06.png)

可以把红色的导航栏去掉，让IDEA显得更加干净整洁一些。使用alt+v，然后去掉Navigation bar即可。去掉这个导航栏后，如果你偶尔还是要用的，直接用alt+home就可以临时把导航栏显示出来。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-03-idea-antic/07.png)

如果想让这个临时的导航栏消失的话，直接使用esc快捷键即可。

#### 5、把鼠标定位到project视图里

当工程里的包和类非常多的时候，有时候我们想知道当前类在project视图里是处在哪个位置。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-03-idea-antic/08.png)

上面图中的Test里，你如何知道它是在MavenTest工程里的哪个位置呢？ 可以先使用alt+F1，弹出Select in视图，然后选择Project View中的Project，回车，就可以立刻定位到类的位置了。

#### 6、强大的symbol

如果你依稀记得某个方法名字几个字母，想在IDEA里面找出来，可以怎么做呢？ 直接使用ctrl+shift+alt+n，使用symbol来查找即可。 比如说：

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-03-idea-antic/09.png)