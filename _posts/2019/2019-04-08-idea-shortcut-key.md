---
layout: post
title: IntelliJ IDEA中快速添加main、System、switch-case代码块自动补齐
no-post-nav: true
category: idea
tags: [idea]
keywords: idea
excerpt : IntelliJ IDEA中快速添加main、System、switch-case代码块自动补齐
---

### IntelliJ IDEA中快速添加main、System、switch-case代码块自动补齐

##### 1、File-Settings-Editor-Live Templaters

##### 2、点击+号，新建一个组（在别的组中也可以） 命名为java

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-08-idea-shortcut-key/01.png)

##### 3、选中java，点击+号，新建规则：
- (1)、syso

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-08-idea-shortcut-key/02.png)

代码（模板可自行定义）：
```
syso   syso方法

System.out.println($start$);
```

- (2)、main

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-08-idea-shortcut-key/03.png)

内容如图所示， main则为快捷键 其中$start$为快捷键之后的光标位置。

代码（模板可自行定义）：

```
main   main方法

public static void main(String[] args){
    $start$
}
```

- (3)、sw
![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-08-idea-shortcut-key/04.png)

代码（模板可自行定义）：
```
sw/swi   create switch/case block

switch(object){
    case OBJECT:
        break;
    default:
        break;
}
```

##### 4、最后一步，把模板应用于java ： main-右键-change context
![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-08-idea-shortcut-key/05.png)

输入main, 点击IDEA弹出的提示，则会补全main方法，同理可设置多个快捷键。