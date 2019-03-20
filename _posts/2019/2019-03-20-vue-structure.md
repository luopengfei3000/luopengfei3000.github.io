---
layout: post
title: vue框架介绍
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 什么是vue框架
---

#### 一、什么是vue框架
Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。
   
#### 二、vue框架的特点
- 组件化的开发方式类似于java中的类调用
- 适用于前端部分的MVVM开发模式
- 可使用webpack打包发布

#### 三、vue框架核心组成文件介绍
- App.vue:页面入口文件
- main.js:程序入口文件，加载各种公共组件
- eventBus:作为各个组件的通道
- components包:存放公共组件的文件夹

#### 四、整个前端项目文件介绍
<pre><code>|-- build                            // 项目构建(webpack)相关代码
|   |-- build.js                     // 生产环境构建代码
|   |-- check-version.js             // 检查node、npm等版本
|   |-- dev-client.js                // 热重载相关
|   |-- dev-server.js                // 构建本地服务器
|   |-- utils.js                     // 构建工具相关
|   |-- webpack.base.conf.js         // webpack基础配置
|   |-- webpack.dev.conf.js          // webpack开发环境配置
|   |-- webpack.prod.conf.js         // webpack生产环境配置
|-- config                           // 项目开发环境配置
|   |-- dev.env.js                   // 开发环境变量
|   |-- index.js                     // 项目一些配置变量
|   |-- prod.env.js                  // 生产环境变量
|   |-- test.env.js                  // 测试环境变量
|-- src                              // 源码目录
|   |-- components                   // vue公共组件
|   |-- store                        // vuex的状态管理
|   |-- App.vue                      // 页面入口文件
|   |-- main.js                      // 程序入口文件，加载各种公共组件
|-- static                           // 静态文件，比如一些图片，json数据等
|   |-- data                         // 群聊分析得到的数据用于数据可视化
|-- .babelrc                         // ES6语法编译配置
|-- .editorconfig                    // 定义代码格式
|-- .gitignore                       // git上传需要忽略的文件格式
|-- README.md                        // 项目说明
|-- favicon.ico 
|-- index.html                       // 入口页面
|-- package.json                     // 项目基本信息
</code></pre>
