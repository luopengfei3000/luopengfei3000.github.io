---
layout: post
title: Intellij IDEA酷炫插件系列
no-post-nav: true
category: idea
tags: [idea]
keywords: idea
excerpt : Intellij IDEA酷炫插件系列
---

### Intellij IDEA酷炫插件系列

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/01.png)

- 如上图标注 1 所示，IntelliJ IDEA 对插件进行了很好的分类：
- All plugins 显示所有插件。
- Enabled 显示当前所有已经启用的插件。
- Disabled 显示当期那所有已经禁用的插件。
- Bundled 显示所有 IntelliJ IDEA 自带的插件。
- Custom 显示所有我们自行安装的插件，如果你自己装了很多次插件的话，这个选项会用得比较多。
- 如上图标注 2 所示，启用的插件是被勾选的，如果要禁用一个插件，去掉勾选即可。
- 如上图标注 3 所示，弹出 IntelliJ IDEA 公司自行开发的插件仓库列表，供下载安装。
- 如上图标注 4 所示，弹出插件仓库中所有插件列表供下载安装。
- 如上图标注 5 所示，浏览本地的插件文件进行安装，而不是从服务器上下载并安装。

#### 1、Free Mybatis plugin

mybatis 插件，让你的mybatis.xml像java代码一样编辑。我们开发中使用mybatis时时长需要通过mapper接口查找对应的xml中的sql语句，该插件方便了我们的操作。

安装完成重启IDEA之后，我们会看到code左侧或多出一列绿色的箭头，点击箭头我们就可以直接定位到xml相应文件的位置。
dao文件

**dao文件**
![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/02.png)

**xml文件**
![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/03.png)

#### 2、CamelCase 驼峰命名转换

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/04.png)

**使用方法:** SHIFT + ALT + U 转换你选中的参数名称。

#### 3、CodeGlance
在编辑区的右侧显示的代码地图。

#### 4、Grep console
自定义日志颜色，idea控制台可以彩色显示各种级别的log，安装完成后，在console中右键就能打开。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/05.png)

并且可以设置不同的日志级别的显示样式。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/06.png)

#### 5、mybatis log plugin (mybatis日志插件)

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/07.png)

单击,窗口就出来了.接下来我们使用工具时,只要控制台有sql打印,我们的mybatis sql log 就会自动帮大家把sql和参数进行拼装。

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/08.png)

把sql和参数进行拼装

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/09.png)

#### lombok

##### 一、为什么要使用lombok

项目中经常使用bean，entity等类，绝大部分数据类类中都需要get、set、toString、equals和hashCode方法，尽管IDE工具都会帮我们生成。但自动生成这些代码后，如果bean中的属性一旦有修改、删除或增加时，需要重新生成或删除get/set等方法，给代码维护增加负担。而使用了lombok之后，就不需要编写或生成setter或getter等方法了，而使用lombok注解的方式，在编译生成的字节码文件中就会存在setter/getter等方法，这样就大大减少了代码量，方便了代码的维护。

##### 二、idea中安装lombok插件

![](https://luopengfei3000.github.io/assets/images/2019/idea/2019-04-04-idea-plugins/10.png)

##### 三、插件功能测试
安装完插件之后，在实体类中就可以不在自动生成构造方法了，在使用lombok插件的时候，还要进行添加lombok的Maven依赖，测试代码示例如下

**pom.xml文件加入依赖**

```
<dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
   <version>1.16.18</version>
</dependency>
```

```
package demo;

import lombok.EqualsAndHashCode;
import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Setter
@Getter
@ToString
@EqualsAndHashCode
public class People {
    private String name;
    private int age;
    private String male;
}
```

@Data相当于@Getter @Setter @RequiredArgsContructor @ToString @EqualsAndHashCode 的合集，可以直接采用@Data注解进行使用。

```
package demo;

import lombok.Data;

@Data
public class People {
    private String name;
    private int age;
    private String male;
}
```