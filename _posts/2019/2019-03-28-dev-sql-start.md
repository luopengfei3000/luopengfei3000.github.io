---
layout: post
title: 开发过程中Orcale学习记录
no-post-nav: true
category: orcale
tags: [front-end]
keywords: orcale
excerpt : 开发过程中Orcale学习记录
---

### Orcale sql学习一
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

### Orcale sql学习二

实例一：

如下表格数据：

| str_year(年)| str_quarter(季度)| oversize_quality(超差数量)|
|:-------:|:-------:|:-------:|
| 2015  | 1  | 22  |
| 2015  | 2  | 100 |
| 2015  | 3   | 200 |
| 2015 | 4   | 222 |

通过sql查询如下形式：

| str_year(年)|one_oversize(一季度超差数量)|two_oversize(二季度超差数量)|three_oversize(三季度超差数量)|four_oversize(四季度超差数量)|
|:-------:|:-------:|:-------:|:-------:|:-------:|
| 2015  | 22  | 100  | 200 | 222 |

分析：此查询使用到oracle的行转列函数(pivot)

```
with mcs_data as
 (select t1.str_year,
          t1.str_quarter,
          t1.oversize_quality
    from MCS_PRODUCT_OVERSIZE_COUNT t1
    where t1.str_year = '2015') --此查询查到上图表格一数据

select *
  from mcs_data pivot(sum(oversize_quality) as oversize for str_quarter in(1 as one,
                                                                           2 as two,
                                                                           3 as three,
                                                                           4 as four))
 order by str_year
```

实例二：

如下表格数据：

| str_year(年)| str_quarter(季度)| oversize_quality(超差数量)| scrapped_quality(报废数量)|
|:-------:|:-------:|:-------:|:-------:|
| 2015  | 1  | 22  | 88 |
| 2015  | 2  | 100 | 200|
| 2015  | 3   | 200 | 100|
| 2015 | 4   | 222 | 111|

通过sql查询如下形式：

| str_year(年)|one_oversize(一季度超差数量)|one_scrapped(一季度报废数量)|two_oversize(二季度超差数量)|two_scrapped(二季度报废数量)|three_oversize(三季度超差数量)scrapped|three_scrapped(三季度报废数量)|four_oversize(四季度超差数量)scrapped|four_scrapped(四季度报废数量)|
|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|
| 2015  | 22  | 88  | 100 | 200 |200  | 100  | 222| 111 |

分析：此查询使用到oracle的行转列函数(pivot)

```
with mcs_data as
 (select t1.str_year,
          t1.str_quarter,
          t1.oversize_quality,
          t1.scrapped_quality
    from MCS_PRODUCT_OVERSIZE_COUNT t1
    where t1.str_year = '2015')

select *
  from mcs_data pivot(sum(oversize_quality) as oversize,sum(scrapped_quality) as scrapped for str_quarter in(1 as one,
                                                                                                             2 as two,
                                                                                                             3 as three,
                                                                                                             4 as four))
 order by str_year
```

实例三：

如下表格数据：

|str_year(年)|str_quarter(季度)|mds_item_id(型号)|target_value(目标值)|oversize_quality(超差数量)|scrapped_quality(报废数量)|count_qty(总数量)|
|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|
|2015|1|8a58cbe66ae960a0016ae97b70960402|555|22|88|110|
|2015|2|8a58cbe66ae960a0016ae97b70960402|200|100|200|300|
|2015|3|8a58cbe66ae960a0016ae97b70960402|888|200|100|300|
|2015|4|8a58cbe66ae960a0016ae97b70960402|999|222|111|333|
|2016|2|8a58cbe66ae960a0016ae97b70960402|500|200|100|300|
|2019|1|8a58cbe66ae960a0016ae97b70960402|100|1|1|2|
|2019|1|8a58cbe66ae960a0016ae97b70960402|412|2|2|4|
|2019|1|8a58cbe66ae960a0016ae97b70960402|951|11|10|21|
|2019|1|8a58cbe66ae960a0016ae97a614303fe|600|1|1|2|
|2019|4|8a58cbe66ae960a0016ae97b70960402|300|1|1|2|
|2022|1|8a58cbe66ae960a0016ae97b70960402|500|66666|9999|76665|
|2019|2|8a58cbe66ae960a0016ae97b70960402|852|8888|9999|18887|

通过sql查询如下形式：

![](https://luopengfei3000.github.io/assets/images/2019/database/2019-03-28-dev-sql-start/03.png)

分析：此查询使用到oracle的行转列函数(pivot)

```
with data_list as
 (select t.str_year,
         t.str_quarter,
         t.mds_item_id,
         mi.item_name mds_item_name,
         (select sum(p.target_value) target_value
            from MCS_PRODUCT_OVERSIZE_COUNT p
           where p.str_year = t.str_year
             and p.mds_item_id = t.mds_item_id
           group by p.str_year, p.mds_item_id) target_value,
         sum(t.oversize_quality) as oversize_quality,
         sum(t.scrapped_quality) as scrapped_quality,
         sum(t.count_qty) as count_qty
    from MCS_PRODUCT_OVERSIZE_COUNT t
    left join mds_item mi
      on t.mds_item_id = mi.id
   group by t.str_year, t.str_quarter, t.mds_item_id, mi.item_name)

select *
  from data_list pivot(sum(oversize_quality) as oversize, sum(scrapped_quality) as scrapped, sum(count_qty) as count_qty for str_quarter in(1 as one,
                                                                                                                                            2 as two,
                                                                                                                                            3 as three,
                                                                                                                                            4 as four))
--where条件
 order by str_year
```
### Orcale sql学习三

如下表格数据：

| parent_register_id| attach_size| attach_name|
|:-------:|:-------:|:-------:|
| 4028b88164a73bf70164aca80c5906a4  | 110011  | 测试1.docx  |
| 8a58cbee65137055016513730536014c  | 10023 | 20180802.txt |
| 4028b88164a73bf70164aca80c5906a4  | 11056   | 测试2.docx |

通过sql查询如下形式：

| parent_register_id|ATTACH_MESAGE|
|:-------:|:-------:|
| 4028b88164a73bf70164aca80c5906a4  | 110011,测试1.docx;11056,测试2.docx  | 
| 8a58cbee65137055016513730536014c  | 10023,20180802.txt  | 

分析：此查询使用到oracle的函数LISTAGG('要转化的列','分隔符')

方式一：

1、使用listagg() + group by

```
SELECT s.parent_register_id,
       LISTAGG(s.attach_size || ',' || s.attach_name, ';') within group(order by s.creation_date) AS ATTACH_MESAGE
  FROM Sys_Attachment s
 WHERE S.parent_table_id = 'GK_PROBLEM_MANAGE_FEEDBACK'
 GROUP BY s.parent_register_id;
```
2、使用listagg() + over(partition by ?)

```
SELECT s.parent_register_id,
    LISTAGG(s.attach_size || ',' || s.attach_name, ';') within group(order by s.creation_date) over(partition by s.parent_register_id) AS ATTACH_MESAGE
FROM Sys_Attachment s
WHERE S.parent_table_id = 'GK_PROBLEM_MANAGE_FEEDBACK'
```

    
  
    
