---
layout: post
title: vue项目从部署到启动
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : ①安装node.js②安装cnpm③cnpm安装脚手架vue-cli④构建项目⑤安装项目依赖⑥运行项目
---

#### 一、安装node.js
① 安装node.js，下载相应版本的node.js,下载地址：https://nodejs.org/en/download/，下载完双击安装，点击下一步直到安装完成。<br/>
② 验证Node.js是否安装好，在windows下，win+r召唤出运行窗口，输入cmd打开命令行窗口。输入node -v即可得到对应的Node.js版本。<br/>
   npm包管理器是集成在Node.js中了，所以在安装Node.js的时候就已经自带了npm，输入npm -v可得到npm的版本。<br/>
   输入以下命令npm -g install npm，更新npm至最新版本。
   
#### 二、安装cnpm
① 执行命令 npm install -g cnpm --registry=https://registry.npm.taobao.org ,使用npm的国内镜像（npm 国内镜像 https://npm.taobao.org/）cnpm 命令代替默认的npm命令，增加依赖包加载速度且避免资源限制。

#### 三、cnpm安装脚手架vue-cli
① 在命令行中运行命令 cnpm install -g vue-cli 安装脚手架。

#### 四、构建项目
将vue项目建在F盘的vue-workspace文件夹下，利用命令进入此目录。<br/>
在cmd中输入盘符F:回车即可进入F盘，<br/>
然后执行命令进入F:\vue-workspace路径目录下，<br/>
再输入新建项目命令 vue init webpack **java-vue**(说明：此处为项目名称)，执行后会自动生成vue项目。

#### 五、安装项目依赖
上面脚手架自动生成的vue项目不能直接运行，需要加载上项目需要的依赖包才能运行。<br/>
① 通过在cmd中使用命令先定位到项目所在路径目录下F:\vue-workspace\java-vue，<br/>
② 然后输入命令 cnpm install 安装项目所需的依赖包资源。<br/>
**注意：**（有时会遇到奇怪的报错，然后通过先执行cnpm rebuild node-sass，后执行cnpm install解决，此步骤不是必须的。）

#### 六、运行项目
在cmd中，注意需要使用命令先定位到F:\workspacesvue-workspace\java-vue目录下，然后再输入命令 npm run dev 来运行项目。<br/>
项目运行成功后浏览器访问地址 http://localhost:8080 就可以查看效果啦。