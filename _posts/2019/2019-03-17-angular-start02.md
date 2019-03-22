---
layout: post
title: Angular6学习笔记2：修改Angular组件:app.component
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 修改Angular组件:app.component
---

这个是由一个名称是 “AppComponent” 的 Angular 组件控制的。<br/>
关于组件：组件是 Angular 应用中的基本构造块。 它们在屏幕上显示数据，监听用户输入，并且根据这些输入执行相应的动作。<br/>
打开项目的目录：src/app ， 可以看到AppComponent实现的三个文件：<br/>
- app.component.ts— 组件的类代码，这是用 TypeScript 写的。
- app.component.html— 组件的模板，这是用 HTML 写的。
- app.component.css— 组件的私有 CSS 样式。
### 1.修改组件的标题
1).打开app.component.ts（组件的类文件），将已有的 title 属性的值修改为 'Tour of Heroes'
```
title = 'Tour of Heroes';
```
2) .打开app.component.html（组件的模版文件），将Angular CLI 自动生成的默认模板。改为下列 HTML 内容
```
<h1>{{title}}</h1>
```
注意：双花括号语法是 Angular 的插值绑定语法。 这个插值绑定的意思是把组件的 title 属性的值绑定到 HTML 中的 h1 标记中
### 2.增加应用样式
大多数应用都会让整个应用保持一致的外观。因此，CLI 会生成一个空白的 styles.css 文件。 你可以把全应用级别的样式放进去。<br/>
进入目录： src/styles.css <br/>
改一下样式写入：
```
h1 {
  color: #369;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 250%;
}
h2, h3 {
  color: #444;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: lighter;
}
body {
  margin: 2em;
}
body, input[text], button {
  color: #888;
  font-family: Cambria, Georgia;
}
/* everywhere else */
* {
  font-family: Arial, Helvetica, sans-serif;
}
```
