---
layout: post
title: Angular6学习笔记9：路由
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 路由
---

在一个页面上写一个导航，导航栏中的每一个选项都对应一个页面。

## 1.添加 AppRoutingModule

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

添加一个 @NgModule.exports 数组，其中放上RouterModule。导出 RouterModule 让路由器的相关指令可以在 AppModule 中的组件中使用。

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

## 2.添加路由定义

路由定义 会告诉路由器，当用户点击某个链接或者在浏览器地址栏中输入某个 URL 时，要显示哪个视图。

典型的 Angular 路由（Route）有两个属性：

- path：一个用于匹配浏览器地址栏中 URL 的字符串。
- component：当导航到此路由时，路由器应该创建哪个组件

eg ：当URL 为 localhost:4200/heroes 时，就导航到 HeroesComponent。

- 导入 HeroesComponent，以便能在 Route中引用它。 
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

## 3.RouterModule.forRoot()
此时想要路由生效，就必须先初始化路由，使路由器开始监听浏览器地址栏的变化。

把 RouterModule 添加到 @NgModule.imports 数组中，并用 routes 来配置它。你只要调用 imports 数组中的 RouterModule.forRoot() 函数就行了

```
imports: [ RouterModule.forRoot(routes) ]
```

关于forRoot：因为现在要在应用的顶级配置这个路由器。所以 forRoot() 方法会提供路由所需的服务提供商和指令，还会基于浏览器的当前 URL 执行首次导航。

## 4.添加路由出口 （RouterOutlet）
打开 AppComponent 的模板，把 <app-heroes> 元素替换为 <router-outlet> 元素。

```
{% raw %}<h1>{{title}}</h1>{% endraw %}
<router-outlet></router-outlet>
<app-messages></app-messages>
```

在这里移除&lt;app-heroes&gt;，是因为只有当用户导航到这里时，才需要显示 HeroesComponent。<br/>
&lt;router-outlet&gt;会告诉路由器要在哪里显示路由到的视图。<br/>
保存，刷新浏览器，在localhost：4200后面加上/heroes 就会看到之前的界面了。

## 5.添加路由链接(routerLInk)
在正常的应用中，是不可能然用户去地址栏输入某些地址的，而且用户也不可能知道需要输入什么，用户应该在点击某个按钮或者超级链接的时候，是应用跳转到相应的页面，使路由器自动的去跳转。

在当前的应用中：添加一个&lt;nav&gt;元素，并在其中放一个链接&lt;a&gt;元素，当点击它时，就会触发一个到 HeroesComponent 的导航。

```
{% raw %}<h1>{{title}}</h1>{% endraw %}
<nav>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

routerLink的值为 "/heroes"，路由器会用它来匹配出指向 HeroesComponent 的路由。 routerLink是RouterLink指令的选择器，它会把用户的点击转换为路由器的导航操作。 它是RouterModule中公开的另一个指令。

保存，刷新浏览器，显示出了应用的标题和指向hero列表的链接，但并没有显示hero列表。

点击这个链接。地址栏变成了 /heroes，并且显示出了hero列表。

## 6.添加仪表盘视图
在一个应用中，不可能只有一个页面，再有多个视图的情况下，才能使路由器更好的发挥其作用，使用 CLI 添加一个 DashboardComponent。

```
ng generate component dashboard
```

<label style="color:red">**⚠注意：CLI 生成了 DashboardComponent 的相关文件，并把它声明到 AppModule 中**</label>

修改DashboardComponent模板文件：

```
<h3>Top Heroes</h3>
<div class="grid grid-pad">
  <a *ngFor="let hero of heroes" class="col-1-4">
    <div class="module hero">
      <h4>{{hero.name}}</h4>
    </div>
  </a>
</div>
```

修改DashboardComponent类文件：

```
import { Component, OnInit } from '@angular/core';
import { Hero } from '../hero';
import { HeroService } from '../hero.service';
 
@Component({
  selector: 'app-dashboard',
  templateUrl: './dashboard.component.html',
  styleUrls: [ './dashboard.component.css' ]
})
export class DashboardComponent implements OnInit {
  heroes: Hero[] = [];
 
  constructor(private heroService: HeroService) { }
 
  ngOnInit() {
    this.getHeroes();
  }
 
  getHeroes(): void {
    this.heroService.getHeroes()
      .subscribe(heroes => this.heroes = heroes.slice(1, 5));
  }
}
```

使DashboardComponent更好看点，增加私有样式

```
[class*='col-'] {
  float: left;
  padding-right: 20px;
  padding-bottom: 20px;
}
[class*='col-']:last-of-type {
  padding-right: 0;
}
a {
  text-decoration: none;
}
*, *:after, *:before {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
h3 {
  text-align: center; margin-bottom: 0;
}
h4 {
  position: relative;
}
.grid {
  margin: 0;
}
.col-1-4 {
  width: 25%;
}
.module {
  padding: 20px;
  text-align: center;
  color: #eee;
  max-height: 120px;
  min-width: 120px;
  background-color: #607d8b;
  border-radius: 2px;
}
.module:hover {
  background-color: #eee;
  cursor: pointer;
  color: #607d8b;
}
.grid-pad {
  padding: 10px 0;
}
.grid-pad > [class*='col-']:last-of-type {
  padding-right: 20px;
}
@media (max-width: 600px) {
  .module {
    font-size: 10px;
    max-height: 75px; }
}
@media (max-width: 1024px) {
  .grid {
    margin: 0;
  }
  .module {
    min-width: 60px;
  }
}
```

解释：

DashboardComponent模板用来表示由英雄名字链接组成的一个阵列。

- *ngFor 复写器为组件的 heroes 数组中的每个条目创建了一个链接。

- 这些链接被 dashboard.component.css 中的样式格式化成了一些色块。

DashboardComponent类和 HeroesComponent 类很像。

- 它定义了一个 heroes 数组属性。

- 它的构造函数希望 Angular 把 HeroService 注入到私有的 heroService 属性中。

- 在 ngOnInit() 生命周期钩子中调用 getHeroes。

这个 getHeroes 函数把要显示的英雄的数量缩减为四个（第二、第三、第四、第五）。

## 7.添加仪表盘路由
如果需要导航到仪表盘的视图就需要在路由器中添加一个路由。把 DashboardComponent 导入到 AppRoutingModule 中。

把一个指向 DashboardComponent 的路由添加到 AppRoutingModule.routes 数组中。

```
import { DashboardComponent }   from './dashboard/dashboard.component';
 
....
....
....
 
const routes: Routes = [
  {path: 'heroes', component: HeroesComponent},
  {path: 'dashboard', component: DashboardComponent},
];
```

## 8.添加默认路由
当应用启动时，浏览器的地址栏指向了网站的根路径。它没有匹配到任何现存路由，因此路由器也不会导航到任何地方。
&lt;router-outlet&gt; 下方是空白的。要让应用自动导航到这个仪表盘，请把下列路由添加到 AppRoutingModule.Routes 数组中。

```
const routes: Routes = [
  {path: 'heroes', component: HeroesComponent},
  {path: 'dashboard', component: DashboardComponent},
  {path: '', redirectTo: '/dashboard', pathMatch: 'full'},
];
```

这个路由会把一个与空路径“完全匹配”的 URL 重定向到路径为 '/dashboard' 的路由。

浏览器刷新之后，路由器加载了 DashboardComponent，并且浏览器的地址栏会显示出 /dashboard 这个 URL。

## 9.把仪表盘链接添加到壳组件中
在这个应用中应该允许用户通过点击页面顶部导航区的各个链接在 DashboardComponent 和 HeroesComponent 之间来回导航。

把仪表盘的导航链接添加到壳组件 AppComponent 的模板中，就放在 Heroes 链接的前面。

```
<h1>{{title}}</h1>
<nav>
  <a routerLink="/dashboard">Dashboard</a>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

保存，刷新浏览器，你就能通过点击这些链接在这两个视图之间自由导航了。

## 10.导航到英雄详情
HeroDetailComponent 可以显示所选英雄的详情。 此刻，HeroDetailsComponent 只能在 HeroesComponent 的底部看到

用户应该能通过三种途径看到这些详情。

1. 通过在仪表盘中点击某个英雄。

2. 通过在英雄列表中点击某个英雄。

3. 通过把一个“深链接”URL 粘贴到浏览器的地址栏中来指定要显示的英雄。

## 11.从 HeroesComponent 中删除英雄详情
打开 HeroesComponent 的模板文件(heroes/heroes.component.html)，并从底部删除 &lt;app-hero-detail&gt; 元素。

## 12.添加英雄详情视图
要导航到 id 为 11 的英雄的详情视图，类似于 ~/detail/11 的URL将是一个不错的 URL。

打开 AppRoutingModule 并导入 HeroDetailComponent。

然后把一个参数化路由添加到 AppRoutingModule.routes 数组中，它要匹配指向英雄详情视图的路径。

```
import { HeroDetailComponent }  from './hero-detail/hero-detail.component';
...
...
...
const routes: Routes = [
  {path: 'heroes', component: HeroesComponent},
  {path: 'dashboard', component: DashboardComponent},
  {path: '', redirectTo: '/dashboard', pathMatch: 'full'},
  {path: 'detail/:id', component: HeroDetailComponent},
];
```

<label style="color:red">**⚠注意：path 中的冒号（:）表示 :id 是一个占位符，它表示某个特定英雄的 id**</label>

## 13.DashboardComponent 中的英雄链接

```
<h3>Top Heroes</h3>
<div class="grid grid-pad">
  <a *ngFor="let hero of heroes" class="col-1-4"
     routerLink="/detail/{{hero.id}}">
    <div class="module hero">
      <h4>{{hero.name}}</h4>
    </div>
  </a>
</div>
```

## 14.HeroesComponent 中的英雄链接

```
<h2>My Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <a routerLink="/detail/{{hero.id}}">
      <span class="badge">{{hero.id}}</span> {{hero.name}}
    </a>
  </li>
</ul>
```

## 15.移除死代码
在这个过程中，会有一些之用到，但是现在用不到的代码，虽然代码量很少，但是最好还是将其删除。

## 16.支持路由的 HeroDetailComponent
当路由器会在响应形如 ~/detail/11 的 URL 时创建 HeroDetailComponent。

HeroDetailComponent 需要从一种新的途径获取要显示的英雄。

- 获取创建本组件的路由，

- 从这个路由中提取出 id

- 通过 HeroService 从服务器上获取具有这个 id 的英雄数据。

导入需要的语句，修改构造函数，把 ActivatedRoute、HeroService 和 Location 服务注入到构造函数中，将它们的值保存到私有变量里：

```
import { ActivatedRoute } from '@angular/router';
import { Location } from '@angular/common';
 
import { HeroService }  from '../hero.service';
 
...
...
...
constructor(
  private route: ActivatedRoute,
  private heroService: HeroService,
  private location: Location
) {}
```

<label style="color:blue">**ActivatedRoute**</label> 保存着到这个 HeroDetailComponent 实例的路由信息。 这个组件对从 URL 中提取的路由参数感兴趣。 其中的 id 参数就是要现实的英雄的 id。

<label style="color:blue">**HeroService**</label> 从远端服务器获取英雄数据，本组件将使用它来获取要显示的英雄。

<label style="color:blue">**location**</label> 是一个 Angular 的服务，用来与浏览器打交道。

## 17.从路由参数中提取 id

```
ngOnInit(): void {
  this.getHero();
}
 
getHero(): void {
  const id = +this.route.snapshot.paramMap.get('id');
  this.heroService.getHero(id)
    .subscribe(hero => this.hero = hero);
}
```

route.snapshot 是一个路由信息的静态快照，抓取自组件刚刚创建完毕之后。

paramMap 是一个从 URL 中提取的路由参数值的字典。"id" 对应的值就是要获取的英雄的 id。

路由参数总会是字符串。 JavaScript 的 (+) 操作符会把字符串转换成数字，英雄的 id 就是数字类型。

刷新浏览器，应用挂了。出现一个编译错误，因为 HeroService 没有一个名叫 getHero() 的方法。

## 18.添加 HeroService.getHero()

```
getHero(id: number): Observable<Hero> {
  this.messageService.add(`HeroService: fetched hero id=${id}`);
  return of(HEROES.find(hero => hero.id === id));
}
```

<label style="color:red">**⚠注意：反引号 ( ` ) 用于定义 JavaScript 的 模板字符串字面量，以便嵌入 id**</label>

getHero() 也有一个异步函数签名。它用 RxJS 的 of() 函数返回一个 Observable 形式的模拟英雄数据。

将来可以用一个真实的 Http 请求来重现实现 getHero()，而不用修改调用了它的 HeroDetailComponent。

保存，刷新浏览器，应用又恢复正常了。你可以在仪表盘或英雄列表中点击一个英雄来导航到该英雄的详情视图。

## 19.回到原路

通过点击浏览器的后退按钮，你可以回到英雄列表或仪表盘视图，这取决于你从哪里进入的详情视图。

如果能在 HeroDetail 视图中也有这么一个按钮就更好了。

把一个后退按钮添加到组件模板的底部，并且把它绑定到组件的 goBack() 方法。

src/app/hero-detail/hero-detail.component.html (back button)

```
<button (click)="goBack()">go back</button>
```

在组件类中添加一个 goBack() 方法，利用你以前注入的 Location 服务在浏览器的历史栈中后退一步。

```
goBack(): void {
  this.location.back();
}
```

## 20.增加一些样式。

<label style="color:blue">**AppComponent**</label>

```
h1 {
  font-size: 1.2em;
  color: #999;
  margin-bottom: 0;
}
h2 {
  font-size: 2em;
  margin-top: 0;
  padding-top: 0;
}
nav a {
  padding: 5px 10px;
  text-decoration: none;
  margin-top: 10px;
  display: inline-block;
  background-color: #eee;
  border-radius: 4px;
}
nav a:visited, a:link {
  color: #607d8b;
}
nav a:hover {
  color: #039be5;
  background-color: #cfd8dc;
}
nav a.active {
  color: #039be5;
}
```

<label style="color:blue">**DashboardComponent**</label>

```
[class*='col-'] {
  float: left;
  padding-right: 20px;
  padding-bottom: 20px;
}
[class*='col-']:last-of-type {
  padding-right: 0;
}
a {
  text-decoration: none;
}
*, *:after, *:before {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
h3 {
  text-align: center; margin-bottom: 0;
}
h4 {
  position: relative;
}
.grid {
  margin: 0;
}
.col-1-4 {
  width: 25%;
}
.module {
  padding: 20px;
  text-align: center;
  color: #eee;
  max-height: 120px;
  min-width: 120px;
  background-color: #607d8b;
  border-radius: 2px;
}
.module:hover {
  background-color: #eee;
  cursor: pointer;
  color: #607d8b;
}
.grid-pad {
  padding: 10px 0;
}
.grid-pad > [class*='col-']:last-of-type {
  padding-right: 20px;
}
@media (max-width: 600px) {
  .module {
    font-size: 10px;
    max-height: 75px; }
}
@media (max-width: 1024px) {
  .grid {
    margin: 0;
  }
  .module {
    min-width: 60px;
  }
}
```

<label style="color:blue">**HeroesComponent**</label>

```
.heroes {
  margin: 0 0 2em 0;
  list-style-type: none;
  padding: 0;
  width: 15em;
}
.heroes li {
  position: relative;
  cursor: pointer;
  background-color: #EEE;
  margin: .5em;
  padding: .3em 0;
  height: 1.6em;
  border-radius: 4px;
}
 
.heroes li:hover {
  color: #607D8B;
  background-color: #DDD;
  left: .1em;
}
 
.heroes a {
  color: #888;
  text-decoration: none;
  position: relative;
  display: block;
  width: 250px;
}
 
.heroes a:hover {
  color:#607D8B;
}
 
.heroes .badge {
  display: inline-block;
  font-size: small;
  color: white;
  padding: 0.8em 0.7em 0 0.7em;
  background-color: #607D8B;
  line-height: 1em;
  position: relative;
  left: -1px;
  top: -4px;
  height: 1.8em;
  min-width: 16px;
  text-align: right;
  margin-right: .8em;
  border-radius: 4px 0 0 4px;
}
```

<label style="color:blue">**HeroDetailComponent**</label>

```
label {
  display: inline-block;
  width: 3em;
  margin: .5em 0;
  color: #607D8B;
  font-weight: bold;
}
input {
  height: 2em;
  font-size: 1em;
  padding-left: .4em;
}
button {
  margin-top: 20px;
  font-family: Arial;
  background-color: #eee;
  border: none;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer; cursor: hand;
}
button:hover {
  background-color: #cfd8dc;
}
button:disabled {
  background-color: #eee;
  color: #ccc;
  cursor: auto;
}
```