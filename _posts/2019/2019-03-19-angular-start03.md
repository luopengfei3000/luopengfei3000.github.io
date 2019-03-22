---
layout: post
title: Angular6学习笔记3：创建一个新的Angular组件，并将它展示出来
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 创建一个新的Angular组件，并将它展示出来
---

### 1.创建一个新的Angular组件
在命令行中(注意要切换都项目路径下，如使用VS code在下方的Terminal中直接输入命令即可)使用 Angular CLI 创建一个名为 heroes 的新组件。
```
ng generate component heroes
```
执行完以后会提示：

![](https://luopengfei3000.github.io/assets/images/2019/angular/2019-03-20-angular-start03/01.png)

```
Microsoft Windows [版本 6.1.7601]
版权所有 (c) 2009 Microsoft Corporation。保留所有权利。

E:\angular-workspace\new-angularaa>ng generate component heroes
CREATE src/app/heroes/heroes.component.html (25 bytes)
CREATE src/app/heroes/heroes.component.spec.ts (628 bytes)
CREATE src/app/heroes/heroes.component.ts (269 bytes)
CREATE src/app/heroes/heroes.component.css (0 bytes)
UPDATE src/app/app.module.ts (475 bytes)
```

这个过程中：CLI 创建了一个新的文件夹src/app/heroes/，并生成了HeroesComponent的三个文件。
打开HeroesComponent的类文件（heroes.component.ts）：
- selector— 组件的选择器（CSS 元素选择器）
- templateUrl— 组件模板文件的位置。
- styleUrls— 组件私有 CSS 样式表文件的位置。

CSS 元素选择器 app-heroes 用来在父组件的模板中匹配 HTML 元素的名称，以识别出该组件。<br/>
ngOnInit是一个生命周期钩子，Angular 在创建完组件后很快就会调用ngOnInit。这里是放置初始化逻辑的好地方。<br/>
始终要 export 这个组件类，以便在其它地方（比如AppModule）导入它。<br/>

### 2.在新建的组件中添加一个属性
在HeroesComponent的类文件（heroes.component.ts）中添加一个属性
```
hero = 'Windstorm';
```
在HeroesComponent的模板文件（heroes.component.html）中展示这个属性
```
{% raw %}{{hero}}{% endraw %}
```

在heroes组件的类文件（heroes.component.ts）中selector（组件的选择器）中的：“app-heroes”就是这个heroes组件的元素选择器，所以显示heroes组件，将<app-heroes> 元素添加到 AppComponent 的模板文件（app.component.html）中就可以了。<br/>
在src/app/app.component.html中：
```
<h1>{% raw %}{{title}}{% endraw %}</h1>
<app-heroes></app-heroes>
```
### 3.创建Hero类
切换到src/app，命令行中(注意要切换都项目路径下，如使用VS code在下方的Terminal中直接输入命令即可)使用 Angular CLI 创建一个名为 hero 的新类。
```
E:\angular-workspace\new-angularaa>ng generate class hero
CREATE src/app/hero.spec.ts (146 bytes)
CREATE src/app/hero.ts (22 bytes)
```
这样在src/app目录下，就会有一个Hero类，在Hero类中新增两个属性
```
export class Hero {
  id: number;
  name: string;
}
```
切换到HeroesComponent 类中，导入这个Hero类
```
import {Hero} from '../hero';
```
将Heroes组件的 hero 属性的类型重构为 Hero。 然后以 1 为 id、以 “Windstorm” 为名字初始化它。
```
hero: Hero = {
    id: 1,
    name: 'Windstorm',
  };
```
此时，保存以后，页面就会显示对象的类型，因为原来的hero属性是string型，现在是Object型。<br/>
修改HeroesComponent模板中的绑定，以显示hero的名字，并在详情中显示 id 和 name，
```
<h2>{% raw %}{{hero.name}}{% endraw %} Details</h2>
<div><span>id: </span>{% raw %}{{hero.id}}{% endraw %}</div>
<div><span>name: </span>{% raw %}{{hero.name}}{% endraw %}</div>
```