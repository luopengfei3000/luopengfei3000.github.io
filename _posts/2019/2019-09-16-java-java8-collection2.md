---
layout: post
title: java8集合操作
no-post-nav: true
category: java
tags: [java]
keywords: java
excerpt : java8集合操作
---

### java8集合操作

#### list集合操作

##### list字符串集合操作

``` java
     List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
     
     计算空字符串的数量
     long count = strings.stream().filter(string->string.isEmpty()).count();
     
     字符串长度为 3 的数量
     long count = strings.stream().filter(string -> string.length() == 3).count();
     
     筛选后的列表
     List<String> filtered = strings.stream().filter(string ->!string.isEmpty()).collect(Collectors.toList());
    
     筛选后合并字符串
     mergedString = strings.stream().filter(string ->!string.isEmpty()).collect(Collectors.joining(", "));
    
     List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
     
     去重后平方
     List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
     System.out.println("Squares List: " + squaresList);
    
     获取集合中max，min,sum,avg
     IntSummaryStatistics stats = numbers.stream().mapToInt((x) ->x).summaryStatistics();
    
     System.out.println("列表中最大的数 : " + stats.getMax());
     System.out.println("列表中最小的数 : " + stats.getMin());
     System.out.println("所有数之和 : " + stats.getSum());
     System.out.println("平均数 : " + stats.getAverage());
```

``` java
    列表: [abc, , bc, efg, abcd, , jkl]
    空字符串数量为: 2
    字符串长度为 3 的数量为: 3
    筛选后的列表: [abc, bc, efg, abcd, jkl]
    筛选后合并字符串: abc, bc, efg, abcd, jkl
    Squares List: [9, 4, 49, 25]
    列表: [3, 2, 2, 3, 7, 3, 5]
    列表中最大的数 : 7
    列表中最小的数 : 2
    所有数之和 : 25
    平均数 : 3.5714285714285716
```

##### list对象集合操作
``` java

    filter使用（过滤）
    将list中的Student按年龄大于等于15的,升序排列
    调用compare中的排序方法comparing自然排序
    List<Student> student1 = list.stream().filter(x -> x.getAge() >= 20).sorted(Comparator.comparing(Student::getAge)).collect(Collectors.toList());
    System.out.println("list中的Student按年龄大于等于20的升序排列:"+student1.toString());
    
    将list中的Student年龄小于15的,降序排列
    调用Integer中的compare方法
    List<Student> student2 = list.stream().filter(x -> x.getAge() < 14).sorted((s1, s2) -> Integer.compare(s2.getAge(), s1.getAge())).collect(Collectors.toList());
    System.out.println("list中的Student年龄小于15的降序排列:"+student2.toString());
    
    将list中的Student年龄小于15的,并且生日降序排列
    List<Student> student3 = resultList.stream().filter(x -> x.getAge() < 14).
                        sorted(Comparator.comparing(Student::getBirthday,(s1, s2) -> {
                            return s2.compareTo(s1);
                        })).collect(Collectors.toList());
    
    计算list中年龄小于16的元素的个数
    统计功能，一般都是结合filter使用，因为先筛选出我们需要的再统计即可
    long count = list.stream().filter(x -> x.getAge() < 16).count();
    System.out.println("ist中年龄小于16的元素的个数:"+count);
    
    map使用提取单列数据集合（map将对象转换为其他对象）
    取出每个人的年龄,加1后返回成List
    List<Integer> list2 = list.stream().map(x -> x.getAge()).map(i -> i + 1).collect(Collectors.toList());
    System.out.println("每个人的年龄加1后返回成List:"+list2.toString());
    
    获取List中每个人的名字,返回成一个List
    List<String> collect = list.stream().map(x -> x.getName()).collect(Collectors.toList());
    List<String> collect2 = list.stream().map(Student::getName).collect(Collectors.toList());
    List<String> collect3 = list.stream().map(item -> {
    	return item.getName();
    }).collect(Collectors.toList());
    System.out.println("每个人的名字返回一个list集合:"+collect.toString());
    
    扩展：返回的name集合拼接成字符串
    String collect = students.stream().map(item -> item.getName()).collect(Collectors.joining(","));//以,拼接name
    结果：张三,李四，王五
    String names = students.stream().map(Student::getName).collect(Collectors.joining(",","[","]"));//第一个是分界符，第二个是前缀符，第三个是结束符
    结果：[张三,李四，王五]
    
    成绩和年龄之和返回一个list集合
    List<Integer> integers = list.stream().map(student -> {
        return student.getGrade() + student.getAge();
    }).collect(Collectors.toList());
    System.out.println("绩和年龄之和返回一个list集合:"+integers.toString());
    
    计算list中年龄之和
    //Optional allsum = list.stream().map(Student::getAge).reduce((x, y) -> x + y);
    Optional allsum = list.stream().map(Student::getAge).reduce(Integer::sum);
    System.out.println("计算list中年龄之和"+ allsum.get());
    方式二：
    age若为一般类型Integer
    此方式保证age属性全部有值,不可为null
    //Integer ages = list.stream().mapToInt(Student::getAge).sum();
    此方式age属性可以为null
    Integer ages = list.stream().mapToInt(n -> n.getAge() == null ? 0 : n.getAge()).sum();
    方式三：
    Integer reduce = list.stream().parallel().map(Student::getAge).reduce(0, Integer::sum);
    Integer reduce = list.stream().parallel().map(n -> n.getAge() == null ? 0 : n.getAge()).reduce(0, Integer::sum);
    
    查看每一个学生占的分值比重
    Integer ages = list.stream().mapToInt(Student::getAge).sum();
    List<String> collect = list.stream()
                .mapToDouble(s -> s.getGrade() % grades)
                .mapToLong(weight -> (long) (weight * 1))
                .mapToObj(percentage -> percentage + "%")
                .collect(Collectors.toList());
     System.out.println("查看每一个学生占的分值比重"+ collect.toString());
     
    计算list中大于16的人的年龄之和
    Optional sum = list.stream().filter(x -> x.getAge() > 16).map(x -> x.getAge()).reduce((x, y) -> x + y);
    System.out.println("计算list中大于16的人的年龄之和"+sum.get());
    
    将list中大于16的人的年龄各减1,再计算他们的年龄之和
    int sum2 = list.stream().filter(x -> x.getAge() > 16).map(x -> x.getAge() - 1).reduce(0, (x, y) -> x + y);
    System.out.println("list中大于16的人的年龄各减1再计算他们的年龄之和:"+sum2);
    
    方式一：返回最大年龄和最小年龄
    Optional o1 = list.stream().map(x -> x.getAge()).max((x, y) -> Integer.compare(x, y));
    System.out.println("返回最大年龄:"+o1.get());
    o1 = list.stream().map(x -> x.getAge()).max((x, y) -> Integer.compare(y, x));
    System.out.println("返回最小年龄:"+o1.get());
    
    方式二：集合中计算最大年龄和最小年龄
    //Optional o2 = list.stream().map(x -> x.getAge()).reduce((x, y) -> Integer.max(x, y));
    Optional o2 = list.stream().map(x -> x.getAge()).reduce(Integer::max);
    System.out.println("返回最大年龄:"+o2.get());
    o2 = list.stream().map(x -> x.getAge()).reduce(Integer::min);// 使用了Integer中的min方法
    System.out.print("返回最小年龄:"+o2.get());
    
    方式三：集合中计算最大年龄和最小年龄
    max、min接收一个Comparator（例子中使用java8自带的静态函数，只需要传进需要比较值即可。）并且返回一个Optional对象，该对象是java8新增的类，专门为了防止null引发的空指针异常。
    可以使用max.isPresent()判断是否有值；可以使用max.orElse(new Student())，当值为null时就使用给定值；也可以使用max.orElseGet(() -> new Student());这需要传入一个Supplier的lambda表达式。
     
    Optional<Student> max = list.stream().max(Comparator.comparing(x -> x.getAge()));
    Optional<Student> min = list.stream().min(Comparator.comparing(x -> x.getAge()));
    //判断是否有值
    if (max.isPresent()) {//集合不为空，里面有Student对象
        System.out.println("返回最大年龄:"+max.get());
    }else{//集合为空
        
    }
    
    if (min.isPresent()) {
        System.out.println("返回最小年龄:"+min.get());
    }
    
    获取list集合中平均年龄
    Double avgAge = list.stream().collect(averagingInt(Student::getAge));
```

``` java
    list中的Student按年龄大于等于20的升序排列:[Student{age=20, grade=91, name='rr'}, Student{age=21, grade=86, name='pp'}, Student{age=34, grade=44, name='pp'}]
    list中的Student年龄小于15的降序排列:[Student{age=13, grade=74, name='kk'}, Student{age=12, grade=88, name='aa'}, Student{age=12, grade=52, name='ff'}]
    ist中年龄小于16的元素的个数:4
    每个人的年龄加1后返回成List:[13, 18, 15, 13, 17, 14, 19, 21, 20, 22, 35]
    每个人的名字返回一个list集合:[aa, cc, bb, ff, ss, kk, nn, rr, ww, pp, pp]
    绩和年龄之和返回一个list集合:[100, 116, 99, 64, 108, 87, 71, 111, 98, 107, 78]
    计算list中年龄之和196
    查看每一个学生占的分值比重[88%, 99%, 85%, 52%, 92%, 74%, 53%, 91%, 79%, 86%, 44%]
    计算list中大于16的人的年龄之和129
    list中大于16的人的年龄各减1再计算他们的年龄之和:123
    返回最大年龄:34
    返回最小年龄:12
    返回最大年龄:34
    返回最小年龄:12
    返回最大年龄:34
    返回最小年龄:12
```

##### stream的 count，anyMatch，allMatch，noneMatch操作
```
    stream的 count，anyMatch，allMatch，noneMatch操作，我们先看下函数的定义
    long count();  
    boolean anyMatch(Predicate<? super T> predicate);  
    boolean allMatch(Predicate<? super T> predicate);  
    boolean noneMatch(Predicate<? super T> predicate);
    count方法，跟List接口的size一样，返回的都是这个集合流的元素的长度，不同的是，流是集合的一个高级工厂，中间操作是工厂里的每一道工序，我们对这个流操作完成后，可以进行元素的数量的和；
    anyMatch表示，判断的条件里，任意一个元素成功，返回true
    allMatch表示，判断条件里的元素，所有的都是，返回true
    noneMatch跟allMatch相反，判断条件里的元素，所有的都不是，返回true
```

##### 字符串集合
``` java
    List<String> list = Arrays.asList("张三", "李四", "王五", "马六9");
    long count = list.stream().filter(m -> m.length() == 2).count();
    boolean allMatch = list.stream().allMatch(m -> m.equals("张三"));
    boolean allMatch1 = list.stream().allMatch(m -> m.length() == 2); // 长度是否都等于2
    //count = 3
    //allMatch = false
    //allMatch1 = false
    
    boolean anyMatch = list.stream().anyMatch(m -> m.equals("张三"));
    boolean anyMatch1 = list.stream().anyMatch(m -> m.equals("张0"));
    //anyMatch = true
    //anyMatch1 = false
    
    boolean noneMatch = list.stream().noneMatch(m -> m.equals("张三"));
    boolean noneMatch1 = list.stream().noneMatch(m -> m.equals("张0"));
    //noneMatch = false
    //noneMatch1 = true
    //字符串拼接
    String str[] = {"A", "B", "C", "D"};
    String concat = Arrays.stream(str).reduce("", String::concat);
    //concat: ABCD
```

##### 对象集合
``` java
    List<Student> students = Student.generate();
    boolean b = students.stream().anyMatch(m -> m.getAge() > 15);
```
