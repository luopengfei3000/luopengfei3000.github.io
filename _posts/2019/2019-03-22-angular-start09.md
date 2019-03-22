---
layout: post
title: Angular6学习笔记9：路由
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 路由
---

在一个页面上写一个导航，导航栏中的每一个选项都对应一个页面。

### 1.添加 AppRoutingModule

```
E:\angular-workspace\new-angularaa>ng generate module app-routing --flat --module=app
CREATE src/app/app-routing.module.spec.ts (308 bytes)
CREATE src/app/app-routing.module.ts (194 bytes)
UPDATE src/app/app.module.ts (718 bytes)
```

注意：

--flat 把这个文件放进了 src/app 中，而不是单独的目录中。<br>
--module=app 告诉 CLI 把它注册到 AppModule 的 imports 数组中。

通常情况下不会在路由模块中声明组件，所以可以删除 @NgModule.declarations 并删除对 CommonModule 的引用。

在路由的模块中，通常会使用：RouterModule中的Routes类来配置路由器，所以要从@angular/router 中引入对这个两个符号的引入

添加一个 @NgModule.exports 数组，其中放上RouterModule。导出 RouterModule 让路由器的相关指令可以在 AppModule 中的组件中使用。

此时的AppRoutingModule ：

```
import {NgModule} from '@angular/core';
import {RouterModule, Routes} from '@angular/router';
 
@NgModule({
  imports: [],
  exports: [RouterModule],
})
export class AppRoutingModule {
}
```

### 2.添加路由定义

路由定义 会告诉路由器，当用户点击某个链接或者在浏览器地址栏中输入某个 URL 时，要显示哪个视图。

典型的 Angular 路由（Route）有两个属性：

- path：一个用于匹配浏览器地址栏中 URL 的字符串。
- component：当导航到此路由时，路由器应该创建哪个组件

eg ：当URL 为 localhost:4200/heroes 时，就导航到 HeroesComponent。

- 导入 HeroesComponent，以便能在 Route中引用它。 
- 定义一个路由数组，其中的某个路由是指向这个组件的。

此时的AppRoutingModule ：

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { HeroesComponent } from './heroes/heroes.component';//文件名符合（首字母大写） + 文件类型

const routes: Routes = [
  {path:'heroes',component:HeroesComponent}//路由定义
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {
  
 }
```

### 3.RouterModule.forRoot()
此时想要路由生效，就必须先初始化路由，使路由器开始监听浏览器地址栏的变化。

把 RouterModule 添加到 @NgModule.imports 数组中，并用 routes 来配置它。你只要调用 imports 数组中的 RouterModule.forRoot() 函数就行了

```
imports: [ RouterModule.forRoot(routes) ]
```

关于forRoot：因为现在要在应用的顶级配置这个路由器。所以 forRoot() 方法会提供路由所需的服务提供商和指令，还会基于浏览器的当前 URL 执行首次导航。

### 4.添加路由出口 （RouterOutlet）
打开 AppComponent 的模板，把 <app-heroes> 元素替换为 <router-outlet> 元素。

```
<h1>{{title}}</h1>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

在这里移除 <app-heroes>，是因为只有当用户导航到这里时，才需要显示 HeroesComponent。<br>
<router-outlet> 会告诉路由器要在哪里显示路由到的视图。<br>
保存，刷新浏览器，在localhost：4200后面加上/heroes 就会看到之前的界面了。

### 5.添加路由链接 (routerLInk)
在正常的应用中，是不可能然用户去地址栏输入某些地址的，而且用户也不可能知道需要输入什么，用户应该在点击某个按钮或者超级链接的时候，是应用跳转到相应的页面，使路由器自动的去跳转。
在当前的应用中：添加一个 <nav> 元素，并在其中放一个链接 <a> 元素，当点击它时，就会触发一个到 HeroesComponent 的导航。 

```
&lt;h1&gt;{{title}}&lt;/h1&gt;
<nav>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

routerLink的值为 "/heroes"，路由器会用它来匹配出指向 HeroesComponent 的路由。 routerLink是RouterLink指令的选择器，它会把用户的点击转换为路由器的导航操作。 它是RouterModule中公开的另一个指令。

保存，刷新浏览器，显示出了应用的标题和指向hero列表的链接，但并没有显示hero列表。

点击这个链接。地址栏变成了 /heroes，并且显示出了hero列表。




