---
layout: post
title: Mybatis中ResutlType和ResultMap的区别
no-post-nav: true
category: mybatis
tags: [mybatis]
keywords: mybatis
---

在使用Mybatis进行查询的时候，在结果封装方面有两个选择：ResultType和ResultMap！ <br/>
首先说一下ResultType，在使用ResultType的时候可以指定返回结果为java中常用的类型，比如字符串、map等，但是一般来说，在项目开发过程中，一般会在java中定义一个bean用来封装表中一条记录中所有的字段信息。以City表为例，表的结构如下：

![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-11-mybatis-ResutlType-ResultMap-difference/01.png)

在java中定义的类如下(去除了一些不重要的代码片段)：

```
public class City {
    private Integer id;

    private String name;

    private String countrycode;

    private String district;

    private Integer population;
}
```

由于在有些数据库中字段名是不区分大小写的，可以说java中City类的属性名称和City表中的字段名称是一一对应的关系，所以运行select的结果的时候，可以直接将查出来的一条记录直接封装成City对象。
如果在City类的定义如下：

```
public class City {
    private Integer city_id;

    private String city_name;

    private String city_countrycode;

    private String city_district;

    private Integer city_population;
}
```

那么在使用resultType的时候，将resultType直接指定为City类，则会出现转换或者映射异常，程序将会报错。这个使用有两种解决办法，一种是使用数据库查询语句中的重命名方式，将查询结果中的各个字段进行重命名。实例如下：

```
select 
    id as city_id                    ,
    name as city_name                ,
    countrycode as city_countrycode  ,
    district as city_district        , 
    population as city_population 
from city;
```

使用as关键字将查询结果中的每个字段进行重新命名即可以保持程序正常运行。另一种方法就是使用resultMap，建立一种字段与类中属性的映射关系。<br/>
首先需要定义一个结果集，在mapper.xml文件中，定义如下：

```
<resultMap id="BaseResultMap" type="com.entities.City">
    <id column="ID" jdbcType="INTEGER" property="city_id"/>
    <result column="Name" jdbcType="CHAR" property="city_name"/>
    <result column="CountryCode" jdbcType="CHAR" property="city_countrycode"/>
    <result column="District" jdbcType="CHAR" property="city_district"/>
    <result column="Population" jdbcType="INTEGER" property="city_population"/>
</resultMap>
```

然后在select定义语句时，设置resultMap属性值为BaseResultMap即可在查询到结果时，自动将记录中的字段根据BaseResultMap中描述的对应关系，一一封装到City对象中。