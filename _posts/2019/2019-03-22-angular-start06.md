---
layout: post
title: Angular6学习笔记6：主从组件
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 主从组件
---

主从组件

继上一章学习，现在可以在页面上显示一个数组，但是原则上不能将所有特性放在同一个组件中，
因为如果将所有特性放在同一个组件中，当特性特别多时，组件就变得不好维护了。
所以要将大型组件拆分为多个小型组件，使每一个小型组件集中处理某一个特性任务或者工作流，而且这样也便于维护。<br/>

这次要将上一章中的查看hero详情，放置在一个新的，独立，可复用的组件中，集中在新的组件管理和维护hero详情的信息。

### 1.创建一个新的组件
使用 Angular CLI 生成一个名叫 hero-detail 的新组件。
```
E:\angular-workspace\new-angularaa>ng generate component hero-detail
CREATE src/app/hero-detail/hero-detail.component.css (0 bytes)
CREATE src/app/hero-detail/hero-detail.component.html (30 bytes)
CREATE src/app/hero-detail/hero-detail.component.spec.ts (657 bytes)
CREATE src/app/hero-detail/hero-detail.component.ts (288 bytes)
UPDATE src/app/app.module.ts (548 bytes)
```
这样就生成了 HeroDetailComponent 组件所需要的文件，并把它声明在 AppModule 中。

### 2.编写 HeroDetailComponent的模版（hero-detail.component.html）
从 heroes.component.html 模板的底部把表示heros详情的 HTML 代码剪切粘贴到所生成的 heroes-detail.component.html 模板中。<br/>

粘贴过来的html代码中，有selectHero这个属性，由于现在我们展示的不仅仅是被选中的hero，而是需要展示的是每一个hero，这个过程相当于将HeroDetail这个组件的功能扩大化。
所以将其更改为：hero。<br/>

 hero-detail.component.html如下：
 ```
<div *ngIf="hero">
  <h2>{{hero.name | uppercase}} Details</h2>
  <div><span>id: </span>{{hero.id}}</div>
  <div>
    <label>name:
      <input [(ngModel)]="hero.name" placeholder="name"/>
    </label>
  </div>
 
</div>
```

因为HeroDetail重的hero是从其他父组件得到。所以hero 属性必须是一个带有 @Input() 装饰器的输入属性，因为外部的 HeroesComponent 组件将会绑定到它。<br/>

具体步骤：

- 导入Hero

```
import { Hero } from '../hero';
```

- 导入Input符号

```
import { Component, OnInit, Input } from '@angular/core';
```

- 定义hero

```
@Input() hero: Hero;
```

这样，就对heroDetail的类文件写好了，heroDetail所做的工作就是：从其他的父组件中接收一个Hero类型的对象，并将它展示出来

### 3.显示 HeroDetailComponent

现在有HeroDetail组件显示每一个Hero的具体详情信息，而Heroes组件需要显示每一个Hero的详细信息，这个时候，Heroes组件和heroDetail组件就有了主从关系，heroes组件需要将需要展示详情的Hero对象传给具有展示功能的HeroDetail组件

HeroDetailComponent 的选择器是 'app-hero-detail'。 把 <app-hero-detail> 添加到 HeroesComponent 模板的底部，以便把英雄详情的视图显示到那里，并把 HeroesComponent.selectedHero 绑定到该元素的 hero 属性，HeroesComponent模版即

```
<h2>My Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>
<app-hero-detail [hero]="selectedHero"></app-hero-detail>
```

保存浏览器刷新，界面和之前的不会有变化，但是会有一下优点：

1. 通过缩减 HeroesComponent 的职责简化了该组件。

2. 把 HeroDetailComponent 改进成一个功能丰富的英雄编辑器，而不用改动父组件 HeroesComponent。

3. 改进 HeroesComponent，而不用改动英雄详情视图。

4. 将来可以在其它组件的模板中重复使用 HeroDetailComponent。
