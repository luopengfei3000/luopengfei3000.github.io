---
layout: post
title: angular项目从部署到启动
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : ①安装node.js②安装cnpm③ypeScript的安装（可选）④cnpm安装脚手架Angular-cli⑤构建项目⑥安装项目依赖...
---

#### 一、安装node.js
① 安装node.js，下载相应版本的node.js,下载地址：https://nodejs.org/en/download/，下载完双击安装，点击下一步直到安装完成。<br/>
② 验证Node.js是否安装好，在windows下，win+r召唤出运行窗口，输入cmd打开命令行窗口。输入node -v即可得到对应的Node.js版本。<br/>
   npm包管理器是集成在Node.js中了，所以在安装Node.js的时候就已经自带了npm，输入npm -v可得到npm的版本。<br/>
   输入以下命令npm -g install npm，更新npm至最新版本。
   
#### 二、安装cnpm
① 执行命令 npm install -g cnpm --registry=https://registry.npm.taobao.org ,使用npm的国内镜像（npm 国内镜像 https://npm.taobao.org/）cnpm 命令代替默认的npm命令，增加依赖包加载速度且避免资源限制。

#### 三、TypeScript的安装（可选）
typescript，是ng应用开发中使用的主语言，所以也需要安装ts。打开cmd，输入命令行‘npm install –g typescript’<br/>
验证 'tsc -v' 安装情况 


#### 四、cnpm安装脚手架Angular-cli
Cli是Command Line Interface的简写，是一种命令行接口，实现自动化开发流程。它是ng执行开发、测试、打包和发布必备的集成化平台，俗称脚手架。<br/> 
① 全局安装‘npm install –g @angular/cli’。<br/>
② 检查脚手架版本‘ng -v’。  

#### 五、构建项目
将angular项目建在F盘的angular-workspace文件夹下，利用命令进入此目录。<br/>
在cmd中输入盘符F:回车即可进入F盘，<br/>
然后执行命令进入F:\angular-workspace路径目录下，<br/>
再输入新建项目命令 ng new **java-angular**(说明：此处为项目名称)，执行后会自动生成angular项目。

#### 六、安装项目依赖
上面脚手架自动生成的vue项目不能直接运行，需要加载上项目需要的依赖包才能运行。<br/>
① 通过在cmd中使用命令先定位到项目所在路径目录下F:\angular-workspace\java-angular，<br/>
② 然后输入命令 cnpm install 安装项目所需的依赖包资源。<br/>
**注意：**（有时会遇到奇怪的报错，然后通过先执行cnpm rebuild node-sass，后执行cnpm install解决，此步骤不是必须的。）

#### 七、运行项目
在cmd中，注意需要使用命令先定位到F:\workspacesvue-workspace\java-angular目录下，然后再输入命令 ng serve 来运行项目。<br/>
项目运行成功后浏览器访问地址 http://localhost:4200 就可以查看效果啦。