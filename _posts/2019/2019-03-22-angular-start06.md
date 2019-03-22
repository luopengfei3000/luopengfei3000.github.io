---
layout: post
title: Angular6学习笔记8：服务(Service)(2)
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 主从组件111111
---

主从组件

继上一章学习，现在可以在页面上显示一个数组，但是原则上不能将所有特性放在同一个组件中，
因为如果将所有特性放在同一个组件中，当特性特别多时，组件就变得不好维护了。
所以要将大型组件拆分为多个小型组件，使每一个小型组件集中处理某一个特性任务或者工作流，而且这样也便于维护。<br/>

这次要将上一章中的查看hero详情，放置在一个新的，独立，可复用的组件中，集中在新的组件管理和维护hero详情的信息。
