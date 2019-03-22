---
layout: post
title: Angular6学习笔记4：修改组件的属性并实时显示(ngModel)
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 修改组件的属性并实时显示(ngModel)
---
上一章可以在一个AppComponent中显示了heroesComponent的属性信息，但是hero的Name往往是可以更改的，并将更改后的数据进行实时显示。

### 1.将hero的Name重构
```
<div>
  <label>name:
    <input [(ngModel)]="hero.name" placeholder="name">
  </label>
</div>
```
**注意：**[(ngModel)] 是 Angular 的双向数据绑定语法。这里把 hero.name 属性绑定到了 HTML 的 input 元素上，以便数据流可以双向流动：从 hero.name 属性流动到 input，并且从 input 流回到 hero.name。<br/>
此时保存文件，浏览器刷新，会什么都没有了，查看浏览器的控制台发现报错了。<br/>
报错的原因：使用了 [(ngModel)] ，ngModel 是一个有效的 Angular 指令，不过它在默认情况下是不可用的，它属于一个可选模块 FormsModule。
### 2.导入FormsModule
> 元数据（metadata）：Angular 需要知道如何把应用程序的各个部分组合到一起，以及该应用需要哪些其它文件和库的信息

有些元数据位于 @Component 装饰器中，把它加到组件类上。另一些关键性的元数据位于 @NgModule 装饰器中。

**最重要的 @NgModule 装饰器位于顶级类 AppModule 上。**

Angular CLI 在创建项目的时候就在 src/app/app.module.ts 中生成了一个 AppModule 类。 
- 打开AppModule（app.module.ts）从 @angular/forms 库中导入FormsModule
```
import { FormsModule } from '@angular/forms';
```
- 在FormsModule 添加到 @NgModule 元数据的 @NgModule.imports 数组中，imports数组中的元素是应用所需的外部模块
```
imports: [
  BrowserModule,
  FormsModule
]
```
在输入框中改变name的值，上面显示的hero的name也会随之改变。

### 3.声明HeroesComponent
每个组件都必须声明在（且只能声明在）一个NgModule中。但是这次在使用HeroesComponent组件时，并没有经历声明组件的过程。<br/>
因为 Angular CLI 在生成 HeroesComponent 组件的时候就自动把它加到了 AppModule 中。<br/>
打开 src/app/app.module.ts 你就会发现 HeroesComponent 已经在顶部导入过了。
```
import { HeroesComponent } from './heroes/heroes.component';
```
HeroesComponent 也已经声明在了 @NgModule.declarations 数组中。declarations中声明组件，指令，和管道 。