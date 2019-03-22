---
layout: post
title: Angular学习笔记5: 在页面中显示列表()*ngFor/*ngIf)
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 在页面中显示列表(*ngFor/*ngIf)
---

上一章将一个Object对象显示在模版文件中(html)，而真正在项目中，数据是来自后端服务器返回的，此时先创建一些模拟的数据。

### 1.创建模拟的数据
在 src/app/ 文件夹中创建一个名叫 mock-heroes.ts 的文件。
```
E:\angular-workspace\new-angularaa>ng generate class mock-heroes
CREATE src/app/mock-heroes.ts (28 bytes)
```
定义一个包含十个Hero(Hero类型)的常量数组 HEROES，并导出它。
```
import {Hero} from './hero';
 
export const HEROES: Hero[] = [
  {id: 11, name: 'Mr. Nice'},
  {id: 12, name: 'Narco'},
  {id: 13, name: 'Bombasto'},
  {id: 14, name: 'Celeritas'},
  {id: 15, name: 'Magneta'},
  {id: 16, name: 'RubberMan'},
  {id: 17, name: 'Dynama'},
  {id: 18, name: 'Dr IQ'},
  {id: 19, name: 'Magma'},
  {id: 20, name: 'Tornado'}
];
 
export class MockHeroes {
 
}
```
### 2.显示创建的数据
在HeroesComponent页面上显示这些数据
- 先在HeroesComponent类文件中导入在mock-heroes.ts 的HEROES
```
import {HEROES} from '../mock-heroes';
```
- 在HeroesComponent类文件中添加一个属性：heroes，使其等于导入的HEROES数组，这样可以在HeroesComponent类文件中使用HEROES数组
```
heroes = HEROES;
```
### 3.使用*ngFor将这些数据依次展示出来
打开 HeroesComponent 的模板文件(html)，并做如下修改：
```
<h2>My Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>
```
*ngFor 是一个 Angular 的复写器（repeater）指令。 它会为列表中的每项数据复写它的宿主元素。

- <code>&lt;li&gt;</code> 就是 *ngFor 的宿主元素（会将<code>&lt;li&gt;</code>标签中包含的所有元素全部遍历）。

- heroes 就是来自 HeroesComponent 类的列表。

- 当依次遍历heroes这个列表时，hero 会为每个迭代保存当前的数据对象。

<label style="color:red">注意：⚠️ *ngFor的   *  不能丢掉</label>

### 4.为HeroesComponent添加私有样式
在此之前，在 styles.css 中为整个应用设置了一些基础的样式，但是那些并不适用于现在这个列表，所以，要为这些列表写自己的私有的样式。

定义私有样式的方式：
- 内联在 @Component.styles 数组中
```
import {Component, OnInit} from '@angular/core';
import {Hero} from '../hero';
import {HEROES} from '../mock-heroes';
 
@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styles: ['li{background-color: aliceblue}']
})
export class HeroesComponent implements OnInit {
  hero: Hero = {
    id: 1,
    name: 'Windstorm',
  };
  heroes = HEROES;
 
  constructor() {
  }
 
  ngOnInit() {
  }
 
}
```
- 或者在 @Component.styleUrls 所指出的样式表文件中。
```

import {Component, OnInit} from '@angular/core';
import {Hero} from '../hero';
import {HEROES} from '../mock-heroes';
 
@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})
export class HeroesComponent implements OnInit {
  hero: Hero = {
    id: 1,
    name: 'Windstorm',
  };
  heroes = HEROES;
 
  constructor() {
  }
 
  ngOnInit() {
  }
 
}
```
在这里，用第二种方式(推荐)去添加样式，打开heroes.component.css文件中添加一下样式：
```
.selected {
  background-color: #CFD8DC !important;
  color: white;
}
.heroes {
  margin: 0 0 2em 0;
  list-style-type: none;
  padding: 0;
  width: 15em;
}
.heroes li {
  cursor: pointer;
  position: relative;
  left: 0;
  background-color: #EEE;
  margin: .5em;
  padding: .3em 0;
  height: 1.6em;
  border-radius: 4px;
}
.heroes li.selected:hover {
  background-color: #BBD8DC !important;
  color: white;
}
.heroes li:hover {
  color: #607D8B;
  background-color: #DDD;
  left: .1em;
}
.heroes .text {
  position: relative;
  top: -3px;
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
  margin-right: .8em;
  border-radius: 4px 0 0 4px;
}
```

### 5.使用户在主列表中点击一个hero时，该组件应该在页面底部显示所选hero的详情
- 用户点击，就要添加一个点击事件

<pre class="has">
<code class="language-html">&lt;li *ngFor="let hero of heroes" (click)="onSelect(hero)"&gt;</code></pre>
```
<li *ngFor="let hero of heroes" (click)="onSelect(hero)">
```
click 外面的圆括号会让 Angular 监听这个 <code>&lt;li&gt;</code> 元素的 click 事件。 当用户点击 <code>&lt;li&gt;</code> 时，Angular 就会执行函数 onSelect(hero)。

- 添加 click 事件处理器

添加一个selectedHero标示被选中的hero，在onSelect方法中，将选中的那个hero赋值给selectHero。
```
selectedHero: Hero;
 
onSelect(hero: Hero): void {
  this.selectedHero = hero;
}
```
- 修改模版
```
<h2>My Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes" (click)="onSelect(hero)">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>
 
<h2>{{selectedHero.name}} Details</h2>
<div><span>id: </span>{{selectedHero.id}}</div>
<div>
  <label>name:
    <input [(ngModel)]="selectedHero.name" placeholder="name">
  </label>
</div>
```

**保存刷新浏览器，不正常显示了，打开控制台发现保错：**

这里是因为：当启动应用时，selectedHero这个属性还没有初始化，此时的selectedHero=undefined，此时就用*ngIf 当selectedHero===undefined时，让这段隐藏，在selectedHero！==undefined时，在显示出来。用一个<code>&lt;div&gt;</code><code>&lt;/div&gt;</code>将其包起来。

<label style="color:red">注意：⚠️ *ngIf的 * 不能丢掉</label>

具体如下：
```

<div *ngIf="selectedHero">
  <h2>{{selectedHero.name}} Details</h2>
  <div><span>id: </span>{{selectedHero.id}}</div>
  <div>
    <label>name:
      <input [(ngModel)]="selectedHero.name" placeholder="name">
    </label>
  </div>
</div>
```

### 6.给选中的hero添加样式
Angular 的CSS类绑定机制让根据条件添加或移除一个 CSS 类变得很容易。 只要把 [class.some-css-class]="some-condition" 添加到你要施加样式的元素上就可以了。

在 HeroesComponent 模板中的 <code>&lt;li&gt;</code> 元素上添加 [class.selected] 绑定，即：

```
[class.selected]="hero === selectedHero"
```