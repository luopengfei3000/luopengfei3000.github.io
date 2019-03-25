---
layout: post
title: Angular6学习笔记9：HTTP(1)
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : HTTP
---
在一个实际的项目中，数据使来自服务器上，在Angular中，Angular通过HttpClient与远端服务器进行通讯。

## 模拟数据服务器
使用 内存 Web API（In-memory Web API） 模拟出的远程数据服务器通讯（在实际项目中不使用这个模块，在这里只是模拟后端数据）。

## a.安装in-memory-web-api

```
E:\angular-workspace\new-angularaa>npm install angular-in-memory-web-api --save
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.7 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.7: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ angular-in-memory-web-api@0.8.0
added 1 package and audited 42609 packages in 39.343s
found 0 vulnerabilities
```

### b.创建一个数据服务：in-memory-data

```
E:\angular-workspace\new-angularaa>ng generate service InMemoryData
CREATE src/app/in-memory-data.service.spec.ts (365 bytes)
CREATE src/app/in-memory-data.service.ts (141 bytes)
```

在app.module.ts中引入InMemoryDataService

```
import {InMemoryDataService} from './in-memory-data.service';
```

### c.修改in-memory-data.service.ts文件如下

```
import { Injectable } from '@angular/core';
import { InMemoryDbService } from 'angular-in-memory-web-api';

@Injectable({
  providedIn: 'root'
})
export class InMemoryDataService implements InMemoryDbService{

  createDb() {
    const heroes = [
      { id: 11, name: 'Mr. Nice' },
      { id: 12, name: 'Narco' },
      { id: 13, name: 'Bombasto' },
      { id: 14, name: 'Celeritas' },
      { id: 15, name: 'Magneta' },
      { id: 16, name: 'RubberMan' },
      { id: 17, name: 'Dynama' },
      { id: 18, name: 'Dr IQ' },
      { id: 19, name: 'Magma' },
      { id: 20, name: 'Tornado' }
    ];
    return {heroes};
  }

  constructor() { }
}
```

这个文件代替了之前的 mock-heroes.ts，此时这个时候就可以安全的删除mock-heroes.ts。

当在后期可以从服务器获取数据的时候，就可以删除现在这个文件了。

### d.在app.module.ts 导入HttpClientInMemoryWebApiModule，HttpClient

```
import { HttpClientInMemoryWebApiModule } from 'angular-in-memory-web-api';
import {HttpClientModule} from '@angular/common/http';
```
### e.把 HttpClientInMemoryWebApiModule 添加到 @NgModule.imports 数组中（放在 HttpClient 之后）， 然后使用 InMemoryDataService 来配置它。

```
HttpClientModule,
HttpClientInMemoryWebApiModule.forRoot(InMemoryDataService, {dataEncapsulation: false}),
```

forRoot() 配置方法接受一个 InMemoryDataService 类（初期的内存数据库）作为参数。

## Hero与HTTP

### a.在hero.service.ts导入一些需要的HTTP符号：

```
import { HttpClient, HttpHeaders } from '@angular/common/http';
```

### b.把 HttpClient 注入到构造函数中一个名叫 http 的私有属性中。

```
constructor(
  private http: HttpClient,
  private messageService: MessageService) { }
```

### c.保留对 MessageService 的注入。你将会频繁调用它，因此请把它包裹进一个私有的 log 方法中。

```
private log(message: string) {
  this.messageService.add(`HeroService: ${message}`);
}
```

### d.把服务器上英雄数据资源的访问地址定义为 heroesURL。

```
private heroesUrl = 'api/heroes';
```

## 通过 HttpClient 获取英雄

修改当前的 HeroService.getHeroes() 使用 HttpClient 来模拟英雄数据返回为 Observable<Hero[]> 格式。

```
getHeroes (): Observable<Hero[]> {
  return this.http.get<Hero[]>(this.heroesUrl)
}
```

保存，刷新浏览器，应用就会模拟服务器被成功读取。

但是在这里并没有修改查看hero详情的函数，但是在此刻应用并没有报错。这里是因为： of / HttpClient  返回的都是Observable<Hero[]>。

## Http 方法返回单个值
所有的 HttpClient 方法都会返回某个值的 RxJS Observable。

HTTP 是一个请求/响应式协议。你发起请求，它返回单个的响应。

通常，Observable 可以在一段时间内返回多个值。 但来自 HttpClient 的 Observable 总是发出一个值，然后结束，再也不会发出其它值。

具体到这次 HttpClient.get 调用，它返回一个 Observable<Hero[]>，在这里“返回的这个Hero数组是可观察对象”。在实践中，它也只会返回一个英雄数组。

## HttpClient.get 返回响应数据
HttpClient.get 默认情况下把响应体当做无类型的 JSON 对象进行返回。 如果指定了可选的模板类型 <Hero[]>，就会给返回你一个类型化的对象。JSON 数据的具体形态是由服务器的数据 API 决定的。 英雄指南的数据 API 会把英雄数据作为一个数组进行返回。

## 错误处理
有时候在从后端获取数据，可能会因为一些其他原因，发生一些错误。此刻，在HttpClient.get（）方法中应该捕获错误，并做适当的处理，如果要捕获错误，就需要使用 RxJS 的 catchError() 操作符来建立对 Observable 结果的处理管道（pipe）。

从 rxjs/operators 中导入 catchError 符号。

```
import { catchError} from 'rxjs/operators';
```

使用 .pipe() 方法来扩展 Observable 的结果，并给它一个 catchError() 操作符

```
getHeroes (): Observable<Hero[]> {
  return this.http.get<Hero[]>(this.heroesUrl)
    .pipe(
      catchError(this.handleError('getHeroes', []))
    );
}
```

此时在this.handleError('getHeroes',[]),这里会报错，是因为，暂时还没有声明这个方法。

catchError() 操作符会拦截失败的 Observable。 它把错误对象传给错误处理器，错误处理器会处理这个错误。

## handleError
在实际的项目中，很多component都需要从远端服务器获取数据，这过程中可能会需要很多的请求，而这么多的请求中都有可能会发生错误，在发生错误的时候，就需要将用类似的 handleError（）这种方法，如此之来，handleError（） 将会在很多 HeroService 的方法之间共享，所以要把它通用化，以支持这些彼此不同的需求。它不再直接处理这些错误，而是返回给 catchError 返回一个错误处理函数。还要用操作名和出错时要返回的安全值来对这个错误处理函数进行配置。

```
private handleError<T> (operation = 'operation', result?: T) {
  return (error: any): Observable<T> => {
    console.error(error); // log to console instead
    this.log(`${operation} failed: ${error.message}`);
    return of(result as T);
  };
}
```

在这个handleError中，处理错误的方法是：在控制台中汇报了这个错误之后，这个处理器会汇报一个用户友好的消息，并给应用返回一个安全值，使它可以继续工作。因为每个服务方法都会返回不同类型的 Observable 结果，因此 handleError() 也需要一个类型参数，以便它返回一个此类型的安全值，正如应用所期望的那样。

## 窥探 Observable
HeroService 的方法将会窥探 Observable 的数据流，并通过 log() 函数往页面底部发送一条消息。

它们可以使用 RxJS 的 tap 操作符来实现，该操作符会查看 Observable 中的值，使用那些值做一些事情，并且把它们传出来。 这种 tap 回调不会改变这些值本身。

### a.在HeroService中导入：tap

```
import {catchError, tap} from 'rxjs/operators';
```

### b.修改getHeroes();

```
getHeroes(): Observable<Hero[]> {
    return this.http.get<Hero[]>(this.heroesUrl)
      .pipe(
        tap(heroes => {
          console.log(heroes);
          this.log('fetched heroes');
        }),
        catchError(this.handleError('getHeroes', []))
      );
  }
```

<label style="color:red">**⚠注意：此时浏览器控制台将打印heroes信息。**</label>

## 通过ID去获取hero信息
在实际项目中，有时候会遇到一个某种一系列类似事物的列表，然后要查看这个一系列中的某一个的详细信息，此时可能就会需要类似于通过 api/hero/:id 的形式（比如 api/hero/:id ）获取这个单个对象的全部信息，修改 HeroService.getHero() 方法来发起请求！

```
getHero(id: number): Observable<Hero> {
  const url = `${this.heroesUrl}/${id}`;
  return this.http.get<Hero>(url).pipe(
    tap(_ => this.log(`fetched hero id=${id}`)),
    catchError(this.handleError<Hero>(`getHero id=${id}`))
  );
}
```

这里的getHeroes()和修改前的相比有三个显著的差异。

1. 它使用想获取的英雄的 id 构建了一个请求 URL。

2. 服务器应该使用单个英雄作为回应，而不是一个英雄数组。

3. 所以，getHero 会返回 Observable<Hero>（“一个可观察的单个hero对象”），而不是一个可观察的hero对象数组。


