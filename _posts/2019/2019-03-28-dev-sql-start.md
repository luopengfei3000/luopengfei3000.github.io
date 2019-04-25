---
layout: post
title: 开发过程中sql学习记录
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : 开发过程中sql学习记录
---

##### 根据图一可以看到查询出6条数据，第一条为树根节点数据，第二条为树二层节点数据，第三条外后为树的三层四层等节点数据

![](https://luopengfei3000.github.io/assets/images/2019/database/2019-03-28-dev-sql-start/01.png)

![](https://luopengfei3000.github.io/assets/images/2019/database/2019-03-28-dev-sql-start/02.png)

    需求：
    1. 只查询树的第三层之外的节点数据（包括第三层数据）
    2. 查询上面条件过滤后数据的节点总数，每条数据有个字段status，当status为25时为已完成，反之为未完成
       已完成数量:节点总数/已完成数量
       未完成数量:节点总数/未完成数量
       
    
第一个条件的sql:

```
select *
  from pmm_project_plan ppp
  left join pmm_project pp
    on ppp.pmm_project_id = pp.id
 where pp.project_numberd_d like '%' || 'wangxiang2' || '%'
 
   and not exists
 (select t.id
          from (select t.id, level as leves
                  from PMM_PROJECT_PLAN t
                connect by prior t.id = t.parent_project_plan_id
                 start with t.parent_project_plan_id = '-1') t
         where leves <= 2
           and t.id = ppp.id)
```

第二个条件的sql：

```
select nums,
      finished || '/' || decode(nums,0,0,round(finished / nums * 100, 2)) || '%' as finished,
      unfinished || '/' || decode(nums,0,0,round(unfinished / nums * 100, 2)) || '%' as unfinished
  from (select count(*) as nums,
               nvl(sum(case status
                     when '25' then
                      1
                     else
                      0
                   end),0) as finished,
               nvl(sum(case status
                     when '25' then
                      0
                     else
                      1
                   end),0) as unfinished
          from (select *
                  from pmm_project_plan ppp
                  left join pmm_project pp
                    on ppp.pmm_project_id = pp.id
                 where pp.project_numberd_d  like '%'||'wangxiang2'||'%'
                 
                   and not exists
                 (select t.id
                          from (select t.id, level as leves
                                  from PMM_PROJECT_PLAN t
                                connect by prior t.id = t.parent_project_plan_id
                                 start with t.parent_project_plan_id = '-1') t
                         where leves <= 2
                           and t.id = ppp.id)))
```

使用到的函数：

1.sum（求和）、nvl（null转0）函数
```
nvl(sum(case status
    when '25' then
       1
    else
       0
    end),0)
```
- 如果status为25时，显示1，反之为0，使用sum相加，可得出状态为25的数量。
- 如果 sum为null时，使用nvl转化为0。（条件没有符合时，sum为null）

2.round（四舍五入）函数
```
round(finished / nums * 100, 2)
```
- 如果 finished / nums * 100 除不尽时，保留两位小数

3.decode函数

```
decode(nums,0,0,round(finished / nums * 100, 2))
```
- decode(除数，0，显示0%，不为零的正常表达式）代替原来那个有可能出0除报错的表达式
- 如果 nums 除数为0，算法报错，因此当除数为0时显示0，反之计算round(finished / nums * 100, 2)



    
  
    
