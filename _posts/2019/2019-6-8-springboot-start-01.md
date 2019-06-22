---
layout: post
title: springboot初始学习
no-post-nav: true
category: springboot
tags: [springboot]
keywords: springboot
excerpt : springboot初始学习
---

## springboot初始学习
1. springboot 优点
* 创建独立的Spring应用程序
* 直接嵌入Tomcat，Jetty或Undertow（无需部署WAR文件）
* 提供自己的“入门”POM来简化您的Maven配置
* 尽可能自动配置Spring
* 提供生产就绪功能，如指标，运行状况检查和外部配置
* 绝对不会生成代码，也不需要XML配置

### SpringBoot启动加载说明

SpringBoot基于main方法启动时，默认会自动扫描本包及本包下所有子包的内容，所以我们将main方法向外提一层；

**例如：**

情况一：`springboot-start01` 模块，启动类 `SpringbootStart01Application` 在包 `com.example.springbootstart01` 下面，则启动项目时，则自动扫描 `com.example.springbootstart01` 包下面的类，如果controller，service类也在 `com.example.springbootstart01` 包路径下，则启动时加载这些类。

情况二：`springboot-start01` 模块，启动类 `SpringbootStart01Application` 在包 `com.example.springbootstart01` 下面，则启动项目时，则自动扫描 `com.example.springbootstart01` 包下面的类，如果controller，service类在 `com.example` 包路径下，则启动时不加载此类。此时需要在启动在 `SpringbootStart01Application` 启动类中加入注解 `@ComponentScan(value="com.example")` ，则启动时加载这些类。

### SpringBoot修改运行时控制台的图标
#### 修改banner
- 在spring boot启动的时候会有有个默认的启动图案，这个图案可以自定义。
- 我们在src/main/resources 下新建一个 Banner.txt。
- 通过 http://patorjk.com/software/taag 网站生成字符，生成后复制到Banner.txt即可，SpringBoot启动时先加载Banner.txt文件

#### 停用banner的三种方法
1. 在 application.yml 或 application.properties文件中配置
- 在.yml 中配置如下：
    ```
    spring:
        main:
            banner-mode: "off"
    ```
- 在.properties 文件中配置如下
    ```
    spring.main.banner-mode = off
    ```
2. 在启动类中设置
- 第一种
    ```
    SpringApplication springApplication = new SpringApplication(Springboot01Application.class);
    springApplication.setBannerMode(Banner.Mode.OFF);
    springApplication.run(args);
    ```
- 第二种
    ```
    new SpringApplicationBuilder(Springboot01Application.class)
            .bannerMode(Banner.Mode.OFF)
            .build()
            .run(args);
    ```
    
### 日志的使用
    
Logback：一款优秀的日志框架
    
SpringBoot的Starter默认日志框架：Logback，默认级别：INFO
    
配置文件：官方推荐名称logback-spring.xml
    
访问localhost:8080/test/log
    
会在项目目录生成一个新目录app_log
    
存在两个文件：一个用来存放ERROR级别信息，一个存放INFO级别和WARN级别
    
    
   

