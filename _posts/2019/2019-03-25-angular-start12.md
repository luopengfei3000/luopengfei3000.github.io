---
layout: post
title: Angular6学习笔记10：HTTP(3)
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : HTTP
---
上一章学习后，可以模拟向后端发送get/post/put/delete请求了。在项目中，有一个table，这个table的数据非常多，就好比现在的heroList，需要根据用户输入的信息发送给远端服务器，让远端服务器通过这个信息，返回搜索结果。现在要检索heroList中的信息，就需要一个输入框，让用户输入检索的值，然后将这个值发送给远端服务器（模拟），然后让远端服务器（模拟）返回检索的结果
  
### 1.在heroService中创建一个关于搜索的方法：searchHeroes（）

```
searchHeroes(term: string): Observable<Hero[]> {
  if (!term.trim()) {
    return of([]);
  }
  return this.http.get<Hero[]>(`${this.heroesUrl}/?name=${term}`).pipe(
    tap(_ => this.log(`found heroes matching "${term}"`)),
    catchError(this.handleError<Hero[]>('searchHeroes', []))
  );
}
```

在这个方法中，当没有搜素词，则返回一个空的数组，当有搜索词的时候，在url中拼接上name

### 2.在仪表盘的组件中添加搜索功能

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
<app-hero-search></app-hero-search>
```

这里会让这个应用挂了，因为找不到<app-hero-search></app-hero-search>（接下来创建）

### 3.创建HeroSearchComponent
利用angular CLI 创建组件

```
E:\angular-workspace\new-angularaa>ng generate component hero-search
CREATE src/app/hero-search/hero-search.component.html (30 bytes)
CREATE src/app/hero-search/hero-search.component.spec.ts (657 bytes)
CREATE src/app/hero-search/hero-search.component.ts (288 bytes)
CREATE src/app/hero-search/hero-search.component.css (0 bytes)
UPDATE src/app/app.module.ts (1258 bytes)
```

CLI 生成了 HeroSearchComponent 的三个文件，并把该组件添加到了 AppModule 的声明中。

修改HeroSearch组件的模版文件：

```
<div id="search-component">
  <h4>Hero Search</h4>
  <input #searchBox id="search-box" (keyup)="search(searchBox.value)" />
  <ul class="search-result">
    <li *ngFor="let hero of heroes$ | async" >
      <a routerLink="/detail/{{hero.id}}">
        {{hero.name}}
      </a>
    </li>
  </ul>
</div>
```

在模版文件中，创建了keyup 事件，这个keyup事件绑定会调用该组件的 search() 方法，并传入新的搜索框的值。

*ngFor 是在一个名叫 heroes$ 的列表上迭代，在这里$ 是一个命名惯例，用来表明 heroes$ 是一个 Observable，而不是数组。

正常情况下，*ngFor是不能直接使用Observable，此时，就要用到管道，利用管道字符（|），后面紧跟着一个 async，它表示 Angular 的 AsyncPipe，AsyncPipe 会自动订阅到 Observable，这样你就不用再在组件类中订阅了。

美化这个HeroSearch组件，修改heroSearch的CSS文件

```
.search-result li {
  border-bottom: 1px solid gray;
  border-left: 1px solid gray;
  border-right: 1px solid gray;
  width:195px;
  height: 16px;
  padding: 5px;
  background-color: white;
  cursor: pointer;
  list-style-type: none;
}
 
.search-result li:hover {
  background-color: #607D8B;
}
 
.search-result li a {
  color: #888;
  display: block;
  text-decoration: none;
}
 
.search-result li a:hover {
  color: white;
}
.search-result li a:active {
  color: white;
}
#search-box {
  width: 200px;
  height: 20px;
}
 
ul.search-result {
  margin-top: 0;
  padding-left: 0;
}
```

修改HeroSearch 的类文件

```
import { Component, OnInit } from '@angular/core';
 
import { Observable, Subject } from 'rxjs';
 
import {
   debounceTime, distinctUntilChanged, switchMap
 } from 'rxjs/operators';
 
import { Hero } from '../hero';
import { HeroService } from '../hero.service';
 
@Component({
  selector: 'app-hero-search',
  templateUrl: './hero-search.component.html',
  styleUrls: [ './hero-search.component.css' ]
})
export class HeroSearchComponent implements OnInit {
  heroes$: Observable<Hero[]>;
  private searchTerms = new Subject<string>();
 
  constructor(private heroService: HeroService) {}
 
  // Push a search term into the observable stream.
  search(term: string): void {
    this.searchTerms.next(term);
  }
 
  ngOnInit(): void {
    this.heroes$ = this.searchTerms.pipe(
      // wait 300ms after each keystroke before considering the term
      debounceTime(300),
 
      // ignore new term if same as previous term
      distinctUntilChanged(),
 
      // switch to new search observable each time the term changes
      switchMap((term: string) => this.heroService.searchHeroes(term)),
    );
  }
}
```

<label style="color:red">**⚠注意：heroes$ 声明为一个 Observable**</label>

#### a.RxJS Subject 类型的 searchTerms

```
private searchTerms = new Subject<string>();
 
search(term: string): void {
  this.searchTerms.next(term);
}
```

每当用户在文本框中输入时，这个事件绑定就会使用文本框的值（搜索词）调用 search() 函数。 searchTerms 变成了一个能发出搜索词的稳定的流。

#### b.串联 RxJS 操作符
每当用户击键后就直接调用 searchHeroes() 将导致创建海量的 HTTP 请求，浪费服务器资源并消耗大量网络流量。

应该怎么做呢？ngOnInit() 往 searchTerms 这个可观察对象的处理管道中加入了一系列 RxJS 操作符，用以缩减对 searchHeroes() 的调用次数，并最终返回一个可及时给出英雄搜索结果的可观察对象（每次都是 Hero[] ）。

```
this.heroes$ = this.searchTerms.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap((term: string) => this.heroService.searchHeroes(term)),
);
```

- 在传出最终字符串之前，debounceTime(300) 将会等待，直到新增字符串的事件暂停了 300 毫秒。 你实际发起请求的间隔永远不会小于 300ms。

- distinctUntilChanged() 会确保只在过滤条件变化时才发送请求。

- switchMap() 会为每个从 debounce 和 distinctUntilChanged 中通过的搜索词调用搜索服务。 它会取消并丢弃以前的搜索可观察对象，只保留最近的。

借助 switchMap 操作符， 每个有效的击键事件都会触发一次 HttpClient.get() 方法调用。 即使在每个请求之间都有至少 300ms 的间隔，仍然可能会同时存在多个尚未返回的 HTTP 请求。

switchMap() 会记住原始的请求顺序，只会返回最近一次 HTTP 方法调用的结果。 以前的那些请求都会被取消和舍弃。

<label style="color:red">**⚠注意：取消前一个 searchHeroes() 可观察对象并不会中止尚未完成的 HTTP 请求。 那些不想要的结果只会在它们抵达应用代码之前被舍弃。**</label>






