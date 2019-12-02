---
layout: post
title: java8集合互相转换
no-post-nav: true
category: java
tags: [java]
keywords: java
excerpt : java8集合互相转换
---

### java8集合互相转换

#### 数组转Map
``` java
String[] str = {"aaa,bbb,ccc,ddd","aa,bb,cc,dd","a,b,c,d"};
Map<String,String> mapMsg = Arrays.stream(str).map(i -> i.split(",")).collect(Collectors.toMap(i->(String)i[1], i->(String)i[3]));
System.out.println(mapMsg.toString());
List<String> moidList = Arrays.stream(str).map(i -> i.split(",")[1]).collect(Collectors.toList());
System.out.println(moidList.toString());
```

``` java
{bb=dd, b=d, bbb=ddd}
[bbb, bb, b]
```

#### 数组转List
``` java
String[] str = {"aaa,bbb,ccc,ddd","aa,bb,cc,dd","a,b,c,d"};
List<String> collect1 = Arrays.stream(str).map(i -> i.split(",")).flatMap(Arrays::stream).collect(Collectors.toList());
List<String> collect2 = Arrays.stream(str).map(i -> i.split(",")).flatMap(str1 -> Arrays.asList(str1).stream()).collect(Collectors.toList());
System.out.println(collect1.toString());
```

``` java
[aaa, bbb, ccc, ddd, aa, bb, cc, dd, a, b, c, d]
```

#### List转Map

1.list转map

``` java
Map<String,Integer> map = list.stream().collect(Collectors.toMap(Person::getName,Person::getAge));
```

2.在转换过程中, key重复的情况的处理方式

``` java
Map<String,Integer> map = list.stream().collect(Collectors.toMap(Person::getName,Person::getAge,(key1,key2) -> key2));
```  

(key1,key2) -> key2是一种表达式, 表示转换map时,如果有两个相同的key,则取后面的key2替换前面的key1,key1,key2两个关键字可以随意修改.

3.key重复时,将前面的value和后面的value相加
 
``` java
Map<String,Integer> map = list.stream().collect(Collectors.toMap(Person::getName,Person::getAge,(key1,key2) -> key1 + key2));
```