---
layout: post
title: Angular6学习笔记10：HTTP(2)
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : HTTP
---
  上一章学习后，可以模拟的调用get方法，并且可以利用某一个ID去查询某一个Hero的详细信息。
  
## 1.修改Hero的信息

在hero-detail的模版文件中，增加一个save按钮，并绑定一个点击事件save()。

```
<button (click)="save()">save</button>
```

实现save()方法：使用heroService中的updateHero()（稍后添加）方法来保存对某一个hero信息修改，然后导航返回之前的一个视图。

``` 
save(): void {
   this.heroService.updateHero(this.hero)
     .subscribe(() => this.goBack());
 }
```

添加updateHero() 方法，在项目中，在进行更新操作的时候，一般情况下发送的是put请求，所以，在这里要调用http.put()方法。

<label style="color:red">**⚠注意：这里的http是HttpClient的实例化的一个对象，将进行的更改保存在远端的服务器上**</label>

```
updateHero (hero: Hero): Observable<any> {
  return this.http.put(this.heroesUrl, hero, httpOptions).pipe(
    tap(_ => this.log(`updated hero id=${hero.id}`)),
    catchError(this.handleError<any>('updateHero'))
  );
}
```

HttpClient.put() 方法接受三个参数

- URL 地址

- 要修改的数据（这里就是修改后的英雄）

- 选项

一般情况，对于一系列中的其中一个事物进行修改，这个时候，可能就需要一个能唯一标示出这个事物的一个参数，常常用的是这个事物的ID，在当前的这个demo中，就可以使用每个hero的id对每个hero进行区别，从而知道具体更新的是哪一个hero。但有时候，（少数情况下）有时会会利用put方法进行新建操作（偶尔使用，不推荐）。

关于参数中的选项：option

有时在传输过程中，需要一个增加一些有特殊意思的请求头，这个时候，就可以在option中使用，就是updateHero方法中的httpOptions

在这里httpOptions：（由于这个httpOptions基本不会再进行改动，所以，用常量const 定义）

```
const httpOptions = {
  headers: new HttpHeaders({ 'Content-Type': 'application/json' })
};
```

此时，保存刷新浏览器，修改一个hero的name，然后点击save按钮，然后在返回的导航中，就可以看到hero修改后的新name。

## 2.新建一个hero
每一个应用的数据不可能仅仅只能修改，当有一个新的hero来到时，这个时候，就需要将新的hero信息增加到当前的hero列表中，我们需要一个输入框，让用户通过这个输入框输入新的hero的name，并在这个输入框的前面提示“Hero Name” ，使用户意识到这个输入的框的意思是输入hero的Name，增加完输入框以后，还需要增加一个按钮,在这个按钮上添加一个点击事件，当用户点击这个按钮的时候，开始获取用户在这个输入框输入的值，并将获取的值发送给远端的服务器，是远端的服务器的到这个新增的值，并添加至相应的数据库中。

### a.在heros的模版文件中,添加input，button等控件。

```
<div>
  <label>Hero name:
    <input #heroName />
  </label>
  <button (click)="add(heroName.value); heroName.value=''">
    add
  </button>
</div>
```

### b.给button上添加点击事件add(heroName.value)，当点击这个button的时候，会触发清空之前填写的数据，当用户输入了数据以后，会将这个数据转换成Hero类似的对象（缺少hero的id属性）。当保存成功以后，会将这个hero的name放入（Array.push()方法）heroes的数组中进行展示。

```
add(name: string): void {
    name = name.trim();
    if (!name) {
      return;
    }
    this.heroService.addHero({name} as Hero)
      .subscribe(hero => {
        this.heroes.push(hero);
      });
  }
```

### c.在httpservice中增加一个addHero()方法，利用http.post()方法向远端服务器进行传输数据(http是HttpClient创建的一个实例)。

```
addHero(hero: Hero): Observable<Hero> {
    return this.http.post<Hero>(this.heroesUrl, hero, httpOptions).pipe(
      tap((hero: Hero) => this.log(`added hero w/ id=${hero.id}`)),
      catchError(this.handleError<Hero>('addHero'))
    );
  }
```

问题：为何此时没有id属性？

在某个一系列的事物中，这种进行事物唯一标示的操作，是来自于远端的服务器后端的分配的，新建的时候，就需要新建的信息发送给后端就可以了，然后远端服务器那边会自动给当前的数据分配一个独一无二的标示(ID)

## 3.删除某一个Hero

当不要这个hero的时候，就需要将这个hero进行删除，将需要删除的hero传给远端的服务器。

在heros的模版文件中,每一个hero<li>标签里增加一个删除的按钮

```
<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <a routerLink="/detail/{{hero.id}}">
      <span class="badge">{{hero.id}}</span> {{hero.name}}
    </a>
    <button class="delete" title="delete hero"
      (click)="delete(hero)">x</button>
  </li>
</ul>
```

使用一些CSS使这个删除按钮浮动在这个<li>的右边。

```
.delete {
  position: relative;
  left: 194px;
  top: -32px;
  background-color: gray !important;
  color: white;
}
```

在类文件中实现删除button中的点击事件delete(hero)方法

```
delete(hero: Hero): void {
  this.heroes = this.heroes.filter(h => h !== hero);
  this.heroService.deleteHero(hero).subscribe();
}
```

虽然这个组件把删除hero的逻辑委托给了 HeroService，但扔保留了更新它自己的hero列表的职责。 组件的 delete() 方法会在 HeroService 对服务器的操作成功之前，先从列表中移除要删除的hero。

组件与 heroService.delete() 返回的 Observable 还完全没有关联。必须订阅它。

在heroService中添加delete()方法

```
 deleteHero(hero: Hero | number): Observable<Hero> {
    const id = typeof hero === 'number' ? hero : hero.id;
    const url = `${this.heroesUrl}/${id}`;
 
    return this.http.delete<Hero>(url, httpOptions).pipe(
      tap(_ => this.log(`deleted hero id=${id}`)),
      catchError(this.handleError<Hero>('deleteHero'))
    );
  }
```

<label style="color:red">**⚠注意：**</label>

- 它调用了 HttpClient.delete。

- URL 就是英雄的资源 URL 加上要删除的英雄的 id。

- 你不用像 put 和 post 中那样发送任何数据。

- 你仍要发送 httpOptions。




