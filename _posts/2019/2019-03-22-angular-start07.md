---
layout: post
title: Angular6学习笔记7：服务(Service)(1)
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 服务(Service)(1)
---

## 问题：为什么需要服务？

因为：组件不应该直接获取或保存数据，它们不应该了解是否在展示假数据。 它们应该聚焦于展示数据，而把数据访问的职责委托给某个服务。

这次将创建一个 HeroService，应用中的所有类都可以使用它来获取英雄列表。 不要使用 new 来创建此服务，而要依靠 Angular 的依赖注入机制把它注入到 HeroesComponent 的构造函数中

**服务是在多个“互相不知道”的类之间共享信息的好办法。**

###　1.创建HeroService
使用 Angular CLI 创建一个名叫 hero 的服务。

```
ng generate service hero
```

```
E:\angular-workspace\new-angularaa>ng generate service hero
CREATE src/app/hero.service.spec.ts (362 bytes)
CREATE src/app/hero.service.ts (133 bytes)
```

执行完这个命令以后，打开src/app/hero.service.ts 中生成 HeroService，

```
import { Injectable } from '@angular/core';
 
@Injectable({
  providedIn: 'root'
})
export class HeroService {
 
  constructor() { }
}
```

#### @Injectable() 服务

这个新的服务导入了 Angular 的Injectable 符号，并且给这个服务类添加了 @Injectable() 装饰器。 它把这个类标记为依赖注入系统的参与者之一。HeroService 类将会提供一个可注入的服务，并且它还可以拥有自己的待注入的依赖。目前它还没有依赖.

@Injectable()装饰器会接受该服务的元数据对象，就像 @Component() 对组件类的作用一样。

### 2.在HeroService中获取hero的数据
HeroService 可以从任何地方获取数据：Web 服务、本地存储（LocalStorage）或一个模拟的数据源。

从组件中移除数据访问逻辑，意味着将来任何时候你都可以改变目前的实现方式，而不用改动任何组件。

在HeroService中导入 Hero 和 HEROES，并写一个获取hero列表的方法：getHeroes()

```
import {Injectable} from '@angular/core';
import {Hero} from './hero';
import {HEROES} from './mock-heroes';
 
@Injectable({
  providedIn: 'root'
})
export class HeroService {
 
  constructor() {
  }
 
  getHeroes(): Hero[] {
    return HEROES;
  }
}
```

### 3.提供（provide） HeroService
在要求 Angular 把 HeroService 注入到 HeroesComponent 之前，你必须先把这个服务提供给依赖注入系统。通过注册提供商来做到这一点。提供商用来创建和交付服务，在这个例子中，它会对 HeroService 类进行实例化，以提供该服务，需要确保 HeroService 已经作为该服务的提供商进行过注册。 你要用一个注入器注册它。注入器就是一个对象，负责在需要时选取和注入该提供商。默认情况下，Angular CLI 命令 ng generate service 会通过给 @Injectable 装饰器添加元数据的形式，为该服务把提供商注册到根注入器上。如果你看看 HeroService 紧前面的 @Injectable() 语句定义，就会发现 providedIn 元数据的值是 'root'，当你在顶层提供该服务时，Angular 就会为 HeroService 创建一个单一的、共享的实例，并把它注入到任何想要它的类上。 在 @Injectable 元数据中注册该提供商，还能让 Angular 可以通过移除那些完全没有用过的服务，来进行优化。

**注意：如果需要，可以在不同的层次上注册提供商 —— 在 HeroesComponent 中、在 AppComponent 中，或在 AppModule 中。 比如，可以通过附加 --module=app 参数来告诉 CLI 要自动在模块级提供该服务。**

```
ng generate service hero --module=app
```

### 4.修改 HeroesComponent
打开 HeroesComponent 类文件。

删除 HEROES 的导入语句，因为你以后不会再用它了。 转而导入 HeroService，把 heroes 属性的定义改为一句简单的声明。

```
import {Component, OnInit} from '@angular/core';
import {Hero} from '../hero';
 
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
  heroes: Hero[];
  selectedHero: Hero;
 
  constructor() {
  }
 
  ngOnInit() {
  }
 
  onSelect(hero: Hero): void {
    this.selectedHero = hero;
  }
}
```

### 5.注入 HeroService
往构造函数中添加一个私有的 heroService，其类型为 HeroService。

```
constructor(private heroService: HeroService) { }
```

添加 getHeroes()

```
getHeroes(): void {
  this.heroes = this.heroService.getHeroes();
}
```

### 6.在 ngOnInit 中调用getHeroes()
#### a.在构造方法中调用（不推荐）
在构造函数调用：getHeroes();

```
constructor(private heroService: HeroService) {
    this.getHeroes();
  }
  ```
  
#### b.在 ngOnInit 生命周期钩子中调用
在ngOnInit 调用：getHeroes();

```
ngOnInit() {
    this.getHeroes();
  }
```

**两者的比较：**

让构造函数保持简单，只做初始化操作，比如把构造函数的参数赋值给属性。 构造函数不应该做任何事。 它肯定不能调用某个函数来向远端服务（比如真实的数据服务）发起 HTTP 请求。

而是选择在 ngOnInit 生命周期钩子中调用 getHeroes()，之后交由 Angular 处理，它会在构造出 HeroesComponent 的实例之后的某个合适的时机调用 ngOnInit。
