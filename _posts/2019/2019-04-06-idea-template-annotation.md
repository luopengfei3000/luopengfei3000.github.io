---
layout: post
title: IntelliJ IDEA 之 注释模板配置
no-post-nav: true
category: idea
tags: [idea]
keywords: idea
excerpt : IntelliJ IDEA 之 注释模板配置
---

### IntelliJ IDEA 之 注释模板配置

#### 1、配置类注释模板

打开File --> Settings设置面板-->Editor-->Live Templates，

点击左侧+号，选择创建模板组，并输入新名称，如下图

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/01.png)

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/02.png)

选中模板组，点击左侧+号，创建模板，如下图

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/03.png)

配置模板的快捷键、描述、模板内容，点击下方Define，选择Java，如下图

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/04.png)

```
/**
 * @classname:
 * @description:
 * @author luopf $Date$
 */
```

配置变量属性值

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/05.png)

点击左下角OK按钮

#### 2、配置方法注释模板
选择自己新建的组名称,点击左侧+号，创建模板，如下图

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/06.png)

配置模板的快捷键、描述、模板内容，点击下方Define，选择Java，如下图

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/07.png)

```
/**
  * @Description
  * @Param  $param$
  * @return $return$
  * @author luopf $date$
  */
```
配置变量属性值

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/08.png)

点击左下角OK按钮

配置方法注解遇到的问题（注意配置好，只能在方法内部使用快捷键生成，然后复制到方法上，不知道IDEA为何这样）
```
/**
 * 跳转到管理页面(采购计划变更审批)
 * @Param request
 * @Param reponse
 * @author luopf 2018/11/20
 */
```
如果想把 @param：每一个参数可以这样

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/09.png)

Edit Template：

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/10.png)

参数值要自己写的
**1、第一种写法**
```
groovyScript("def result='';
def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList();
for(i = 0; i < params.size(); i++)
{result+='* @param ' + params[i] + ((i < params.size() - 1) ? '\\r\\n\\0\\0' : '')};
 return result", methodParameters())
```
**2、第二种写法**
```
groovyScript("def result='';
def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList();
for(i = 0; i < params.size(); i++)
{result+='* @param ' + params[i] + ((i < params.size() - 1) ? '\\n\\b\\0\\0' : '')};
return result", methodParameters())
```

把这个添加进去，但是还要注意一点：

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-06-idea-template-annotation/11.png)