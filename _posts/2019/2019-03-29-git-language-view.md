---
layout: post
title: 修改GitHub上项目语言显示
no-post-nav: true
category: git
tags: [front-end]
keywords: git
excerpt : 修改GitHub上项目语言显示
---

### 修改GitHub上项目语言显示

![](https://luopengfei3000.github.io/assets/images/2019/git/2019-03-29-git-language-view/01.png)

项目显示css文件，而非java<br/>
**使用 .gitattributes配置文件**

![](https://luopengfei3000.github.io/assets/images/2019/git/2019-03-29-git-language-view/02.png)

具体就是在项目根目录添加文件名为.gitattributes的文本文件，写入

```
*.js linguist-language=Java
*.css linguist-language=Java
*.html linguist-language=Java
```

意思就是将.js、css、html当作Java语言来统计。

![](https://luopengfei3000.github.io/assets/images/2019/git/2019-03-29-git-language-view/03.png)


