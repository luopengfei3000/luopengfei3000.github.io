---
layout: post
title: Angular6学习笔记8：服务(Service)(2)
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 服务(Service)(2)
---

可观察的对象（observable）的数据

在现在的是情况下：HeroService.getHeroes() 的函数签名是同步的，它所隐含的假设是 HeroService 总是能同步获取英雄列表数据。 而 HeroesComponent 也同样假设能同步取到 getHeroes() 的结果。但是在实际的项目中，这种情况几乎是不可能实现的，因为，在实际的项目中，这些数据是来源于远端的服务器上，而这个过程始终是一个异步的过程。

在实际的项目中：HeroService 必须等服务器给出响应， 而 getHeroes() 不能立即返回英雄数据， 浏览器也不会在该服务等待期间停止响应。HeroService.getHeroes() 必须具有某种形式的异步函数签名。它可以使用回调函数，可以返回 Promise（承诺），也可以返回 Observable（可观察对象）。

## 一. 重新写一个可观察对象版本的 HeroService

> Observable简介：Observable 是 RxJS中的一个关键类。

### 1.打开 HeroService 文件，并从 RxJS 中导入 Observable 和 of 符号。
```
import { Observable, of } from 'rxjs';
```

### 2. 修改getHeroes 方法。
```
getHeroes(): Observable<Hero[]> {
  return of(HEROES);
}
```

**注意：of(HEROES) 会返回一个 Observable<Hero[]>，它会发出单个值，这个值就是这些模拟英雄的数组。**

###　3.在 HeroesComponent 中订阅 
之前的HeroService.getHeroes 方法返回的是一个 Hero[]，现在它返回的是 Observable<Hero[]>。所以现在必须在 HeroesComponent 中也向本服务中的这种Observable形式变化。

修改getHeroes()：

```
getHeroes(): void {
  this.heroService.getHeroes()
      .subscribe(heroes => this.heroes = heroes);
}
```

Observable.subscribe() 是关键的差异点。

两个版本的差别：

第一个版本：

hero的数组赋值给了该组件的 heroes 属性。 这种赋值是同步的，这里包含的假设是服务器能立即返回hero数组或者浏览器能在等待服务器响应时冻结界面，使其等到后端的数据返回以后，界面在再次解冻。

第二个版本：

应用会等待Observable 发出这个hero数组，这种请求可能立即发生，也可能会在几分钟之后。 然后，subscribe 函数把这个hero数组传给这个回调函数，该函数把hero数组赋值给组件的 heroes 属性。使用这种异步方式，当 HeroService 从远端服务器获取hero数据时，应用就可以工作了。（此过程可以想像类似的想像于将界面暂时冻结，然后等到远端的服务器将需要的数据返回以后，自动的将界面又重新的解冻，使应用重新开始工作）

## 二. 显示信息
学习目标：

- 添加一个 MessagesComponent，它在屏幕的底部显示应用中的消息。

- 创建一个可注入的、全应用级别的 MessageService，用于发送要显示的消息。

- 把 MessageService 注入到 HeroService 中。

- 当 HeroService 成功获取了英雄数据时显示一条消息。

### 1.创建MessagesComponent（推荐使用CLI创建）

```
E:\angular-workspace\new-angularaa>ng generate component messages
CREATE src/app/messages/messages.component.css (0 bytes)
CREATE src/app/messages/messages.component.html (27 bytes)
CREATE src/app/messages/messages.component.spec.ts (642 bytes)
CREATE src/app/messages/messages.component.ts (277 bytes)
UPDATE src/app/app.module.ts (638 bytes)
```

CLI 在 src/app/messages中创建了组件文件，并且把 MessagesComponent 声明在了 AppModule 中。

### 2.修改 AppComponent 的模板来显示所生成的 MessagesComponent

```
<h1>{{title}}</h1>
<app-heroes></app-heroes>
<app-messages></app-messages>
```
<code>&lt;app-messages&gt;</code> 是MessagesComponent的  selector: 'app-messages'

### 3.创建 MessageService

```
E:\angular-workspace\new-angularaa>ng generate service messages
CREATE src/app/messages.service.spec.ts (386 bytes)
CREATE src/app/messages.service.ts (137 bytes)
```

### 4.在MessagesService中增加一个message的变量，并且在写一个新增message的方法和清空message的方法。

```
import {Injectable} from '@angular/core';
 
@Injectable({
  providedIn: 'root'
})
export class MessageService {
 
  // messages 变量
  messages: string[] = [];
 
  // 增加变量的函数
  add(message: string) {
    this.messages.push(message);
  }
 
  // 清空变量的函数
  clear() {
    this.messages = [];
  }
 
  // 构造方法
  constructor() {
  }
}
```

### 5.把它注入到 HeroService 中
打开 HeroService，将导入 MessageService。

```
import { MessageService } from './message.service';
```

### 6.修改这个构造函数
添加一个私有的 messageService 属性参数。 Angular 将会在创建 HeroService 时把 MessageService 的单例注入到这个属性中。

```
constructor(private messageService: MessageService) { }
```

### 7.从 HeroService 中发送一条消息
修改 getHeroes 方法，在获取到英雄数组时发送一条消息。

```
getHeroes(): Observable<Hero[]> {
  this.messageService.add('HeroService: fetched heroes');
  return of(HEROES);
}
```

### 8.从 HeroService 中显示消息
打开 MessagesComponent，并且导入 MessageService。

```
import { MessagesService } from '../messages.service';
```

修改MessagesComponent构造函数，添加一个 public 的 messageService 属性。Angular 将会在创建 MessagesComponent 的实例时把 MessageService 的实例注入到这个属性中。

此处设messageService为public是因为要在messageService在模版文件中用到。

```
constructor(public messagesService: MessagesService) { }
```

<label style="color:red">**注意：⚠️Angular只会绑定公共的属性**</label>

### 9.绑定到 MessageService
修改messageComponent的模版文件

```
<div *ngIf="messageService.messages.length">
  <h2>Messages</h2>
  <button class="clear" (click)="messageService.clear()">clear
  </button>
  <div *ngFor='let message of messageService.messages'> {{message}}</div>
</div>
```

- *ngIf 只有在有消息时才会显示消息区。

- *ngFor用来在一系列 <div> 元素中展示消息列表。

- Angular 的事件绑定把按钮的 click 事件绑定到了 MessageService.clear()这个方法上了。

### 10.Message加css样式
为MessagesComponent组件添加私有样式

```
h2 {
  color: red;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: lighter;
}
body {
  margin: 2em;
}
body, input[text], button {
  color: crimson;
  font-family: Cambria, Georgia;
}
 
button.clear {
  font-family: Arial;
  background-color: #eee;
  border: none;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer;
  cursor: hand;
}
button:hover {
  background-color: #cfd8dc;
}
button:disabled {
  background-color: #eee;
  color: #aaa;
  cursor: auto;
}
button.clear {
  color: #888;
  margin-bottom: 12px;
}
```




