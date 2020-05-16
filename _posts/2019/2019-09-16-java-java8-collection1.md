---
layout: post
title: java8对集合排序/去重/分组
no-post-nav: true
category: java
tags: [java]
keywords: java
excerpt : java8对集合排序/去重/分组
---

### java8对集合排序/去重/分组

#### list集合排序

##### 字符串集合排序

``` java
    List<String> list = new ArrayList<String>();
    list.add("bb");
    list.add("aa");
    list.add("cc");
    list.add("ee");
    list.add("dd");
    Collections.sort(list,(s1,s2) -> s1.compareTo(s2));
```

##### 对象List集合排序

``` java

    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    @Accessors(chain = true)
    public class Student{
        private Integer age;
        private int grade;
        private String name;
        private Date bir;    
    
        public static List<Student> generate() {
            List<Student> list = new ArrayList<Student>();
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
            list.add(new Student(19, "aa", 88, sdf.parse("2020-5-16")));
            list.add(new Student(17, "cc", 99, sdf.parse("2020-5-18")));
            list.add(new Student(14, "bb", 85, null));
            list.add(new Student(12, "ff", 52, sdf.parse("2020-5-16")));
            list.add(new Student(16, "ss", 92, sdf.parse("2020-5-30")));
            list.add(new Student(13, "kk", 74, sdf.parse("2020-6-16")));
            list.add(new Student(18, "nn", 53, sdf.parse("2020-7-16")));
            list.add(new Student(20, "rr", 91, sdf.parse("2020-2-16")));
            list.add(new Student(19, "ww", 79, sdf.parse("2020-1-16")));
            list.add(new Student(21, "pp", 86, sdf.parse("2020-11-16")));
            list.add(new Student(34, "pp", 44, sdf.parse("2020-12-16")));
            return list;
        }
}

```

- 使用lambda排序

```
    List<Student> students = Student.generate();
    对List指定字段进行排序
    1.lambda排序，带参数类型（根据年龄倒叙）
    students.sort((Student stu1, Student stu2) -> stu2.getAge().compareTo(stu1.getAge()));
    2.lambda排序，不带参数类型（按年龄倒叙排）
    students.sort((s1, s2) -> s2.getAge().compareTo(s1.getAge()));
    3.Comparator提供的静态方法
    Collections.sort(students, Comparator.comparing(Student::getGrade).reversed());//按成绩降序（此时比较时，属性可以为一般数据类型）
    Collections.sort(students, Comparator.comparing(Student::getGrade));//按成绩升序（此时比较时，属性可以为一般数据类型）
    Collections.sort(students,(s1,s2) -> s2.getName().compareTo(s1.getName()));
    Collections.sort(students,(s1,s2) -> s1.getAge().compareTo(s2.getAge()));//按年龄倒升序
    如果属性类型为一般数据类型，则通过valueOf转化比较
    Collections.sort(students,(s1,s2) -> Integer.valueOf(s2.getGrade()).compareTo(s1.getGrade()));
    /**
     * Comparator.comparing 方法的使用
     *
     * comparing 方法接收一个 Function 函数式接口 ，通过一个 lambda 表达式传入
     */
    students.sort(Comparator.comparing(e -> e.getAge()));
    /**
     * 该方法引用 Student::getAge 可以代替 lambda表达式
     */
    students.sort(Comparator.comparing(Student::getAge));
    /**
      * 和comparing 方法一不同的是该方法多了一个参数 keyComparator,keyComparator 是创建一个自定义的比较器。
      */
    Collections.sort(students, Comparator.comparing(
                    Student::getName, (s1, s2) -> {
                        return s2.compareTo(s1);
                    }));
    4.组合排序，Comparator提供的静态方法，先按年龄倒排序，年龄相同的按成绩正排序
    Collections.sort(students, Comparator.comparing(Student::getAge).reversed().thenComparing(Student::getGrade));
    组合排序另一种方式写法
    Collections.sort(students, (a, b) -> {
        if (a.getAge() == b.getAge()) {
            return a.getName().compareTo(b.getName());
        } else {
            return a.getAge() - b.getAge();
        }
    });
    students.forEach(System.out::println);
    
    5.使用 Comparator.nullsFirst进行排序
    当集合中存在null元素时，可以使用针对null友好的比较器，null元素排在集合的最前面
    Collections.sort(students, Comparator.nullsFirst(Comparator.comparing(Student::getName)));
    Collections.sort(students, Comparator.nullsLast(Comparator.comparing(Student::getName)));
```
- 使用stream排序

``` java
    List<Student> students = Student.generate();
    1. 按照年龄升序排获取第一个元素
    Student student = students.stream()//findFirst-返回第一个元素//Optional是Java8中避免空指针异常的容器类
                    .sorted((e1, e2) -> Integer.compare(e1.getAge(), e2.getAge()))
                    .findFirst().get();
    1.1 按照名称升序排获取第一个元素
    Student student = students.stream()//findFirst-返回第一个元素//Optional是Java8中避免空指针异常的容器类
                    .sorted((e1, e2) -> e1.getName().compareTo(e2.getName()))
                    .findFirst().get();
    2. 按照成绩升序排
    students = students.stream().sorted(Comparator.comparing(Student::getGrade)).collect(Collectors.toList());
    3. 按照成绩降序排
    students = students.stream().sorted(Comparator.comparing(Student::getGrade).reversed()).collect(Collectors.toList());//按照成绩降序
    4. 按照成绩降序排并按照年龄升序排
    students = students.stream().sorted(Comparator.comparing(Student::getGrade).reversed().
                                thenComparing(Student::getAge)).collect(Collectors.toList());
    
    5. 如果类实现Comparable（implements Comparable<Student>） 并且 重写compareTo方法例如:
    @Override
    public int compareTo(Student ob) {
        return name.compareTo(ob.getName());
    }
    此时，下面方法按照name排序
    students = students.stream().sorted().collect(Collectors.toList());
    students.forEach(System.out::println);
    6. 获取最小日期（日期中有空值则过滤），最高成绩
    Date date = students.stream().map(Student::getBir).filter(item -> item != null).min(Date::compareTo).get();
    Integer integer = students.stream().map(Student::getGrade).max(Integer::compareTo).get();
```

#### list集合去重

``` java
    persons这个list的streamAPI的聚合操作collect可以让我们只关注结果，而collect方法里的collectingAndThen又是属于java.util.stream.Collector，collectingAndThen操作的解释是：先执行前面的操作，然后执行第二部操作后输出结果，这里我们执行的第一步操作就是Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(Person::getName)))，第二步就是将他输出为一个新的ArrayList。
    第一步操作里又是用到Collectors接口，这次用的是toCollection方法，就是将方法里的函数或者参数转化为一个collection集合，这里，我们是将Comparator.comparing(Person::getName)转化为一个collection，这个collection是一个TreeSet。也就是有序的。因为我们需要去掉重复的值，这个set可以做到，而我们又要保持转化出来的collection依旧有序，所以使用的是一个TreeSet。
    Comparator.comparing(Person::getName)这里呢，又用到了java.util.Comparator接口，这个接口倒是挺常用的。使用的是他的comparing方法，也就是比较参数的值是否相同，里面用到的是java8的新特性lambda表达式， :: 其实和.没太大区别，举个例子，最常用的System.out.println() 可以表达为System.out::println，可以达到一样的效果
    
    //将年龄重复的去重
    List<Student> unique = list.stream().collect(
                 Collectors.collectingAndThen(
                         Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(Student::getAge))), ArrayList::new)
         );
    //unique.forEach(p -> System.out.println(p));
    unique.forEach(System.out::println);
```

``` java
    Student{age=12, grade=88, name='aa'}
    Student{age=13, grade=74, name='kk'}
    Student{age=14, grade=85, name='bb'}
    Student{age=16, grade=92, name='ss'}
    Student{age=17, grade=99, name='cc'}
    Student{age=18, grade=53, name='nn'}
    Student{age=19, grade=79, name='ww'}
    Student{age=20, grade=91, name='rr'}
    Student{age=21, grade=86, name='pp'}
    Student{age=34, grade=44, name='pp'}
```

#### list集合分组

``` java
    //根据名称分组
    Map<String, List<Student>> groupName = list.stream().collect(Collectors.groupingBy(Student::getName));
    System.out.println(groupName.toString());
    //根据名称分组求年龄之和
    Map<String, Double> collect = list.stream().collect(Collectors.groupingBy(Student::getName, Collectors.summingDouble(Student::getAge)));
```

``` java
{
    pp=[Student{age=21, grade=86, name='pp'}, Student{age=34, grade=44, name='pp'}], 
    rr=[Student{age=20, grade=91, name='rr'}], 
    nn=[Student{age=18, grade=53, name='nn'}], 
    ff=[Student{age=12, grade=52, name='ff'}], 
    bb=[Student{age=14, grade=85, name='bb'}], 
    ww=[Student{age=19, grade=79, name='ww'}], 
    kk=[Student{age=13, grade=74, name='kk'}], 
    ss=[Student{age=16, grade=92, name='ss'}], 
    cc=[Student{age=17, grade=99, name='cc'}], 
    aa=[Student{age=12, grade=88, name='aa'}]
}
```

#### list集合分组求和

``` java
    /*
    list.add(new User(4.5, "aa"));
    list.add(new User(3.5, "aa"));
    list.add(new User(2.0, "aa"));
    list.add(new User(1.4, "cc"));
    list.add(new User(2.6, "cc"));
    list.add(new User(3.0, "bb"));
    list.add(new User(3.0, "bb"));
    list.add(new User(3.0, "bb"));
    */
    List<User> userGradeList = User.generate();
    Map<String, Double> userGradeMap = new HashMap<String, Double>();
    userGradeList.forEach(userGrade -> userGradeMap.merge(
            userGrade.getName(),
            userGrade.getGrade(),
            Double::sum));
    System.out.println(userGradeMap.toString());
```

``` java
{bb=9.0, cc=4.0, aa=10.0}
```

#### list两个集合合并

``` java
   List<Student> students = new ArrayList<>(3);
   students.add(new Student("路飞", 0));
   students.add(new Student("路飞", 22));
   students.add(new Student("红发", 40));
   students.add(new Student("白胡子", 50, 185));

   List<Student> students1 = new ArrayList<>(2);
   students1.add(new Student("艾斯", 25, 183));
   students1.add(new Student("雷利", 48, 176));

   List<Student> studentList =  Stream.of(students,students1).flatMap(students2 -> students2.stream()).collect(Collectors.toList());

   System.out.println(studentList.toString());
```

``` java
[Student{age=0, grade=0, name='路飞'}, 
Student{age=22, grade=0, name='路飞'}, 
Student{age=40, grade=0, name='红发'}, 
Student{age=50, grade=185, name='白胡子'}, 
Student{age=25, grade=183, name='艾斯'}, 
Student{age=48, grade=176, name='雷利'}]
```

#### list两个集合取差

``` java
    List<Student> generate = Student.generate();
    //集合中全部对象
    System.out.println(generate.size());
    //名称为pp和ww的对象集合
    List<Student> collect = generate.stream().filter(item -> ("pp".equals(item.getName()) || "ww".equals(item.getName()))).collect(Collectors.toList());
    System.out.println(collect.size());
    //去除名称为pp和ww的对象集合，剩余的对象集合
    List<Student> collect1 = generate.stream().filter(item -> !collect.contains(item)).collect(Collectors.toList());
    System.out.println(collect1.size());
```

``` java
    11
    3
    8
```

#### Map集合排序

##### 方式一：
``` java
    Map<Integer, String> map = new HashMap<>();
    map.put(15, "Mahesh");
    map.put(10, "Suresh");
    map.put(30, "Nilesh");
    
    通过map的value排序，并打印(Sort by Map Value)
    map.entrySet().stream().sorted(Comparator.comparing(Map.Entry::getValue))
            .forEach(e -> System.out.println("Key: "+ e.getKey() +", Value: "+ e.getValue()));
    
    通过map的key排序，并打印(Sort by Map Key)
    map.entrySet().stream().sorted(Comparator.comparing(Map.Entry::getKey))
            .forEach(e -> System.out.println("Key: "+ e.getKey() +", Value: "+ e.getValue()));
            
    通过map的key/value排序
    map = map.entrySet().stream().sorted(Comparator.comparing(Map.Entry::getKey))
             .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e2, LinkedHashMap::new));      
    map = map.entrySet().stream().sorted(Comparator.comparing(Map.Entry::getValue))
            .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e2, LinkedHashMap::new));
    
    通过map的key正排序，并返回map集合
    map = map.entrySet().stream().sorted(Map.Entry.comparingByKey())
            .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e2, LinkedHashMap::new));
                    
    通过map的key倒排序，并返回map集合
    map = map.entrySet().stream().sorted(Collections.reverseOrder(Map.Entry.comparingByKey()))
            .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e2, LinkedHashMap::new));
                
    将map的value按升序排序
    map = map.entrySet().stream().sorted(Map.Entry.comparingByValue())
            .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e2, LinkedHashMap::new));
    将map的value按降序排序
    map = map.entrySet().stream().sorted(Collections.reverseOrder(Map.Entry.comparingByValue()))
            .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e2, LinkedHashMap::new));
    System.out.println(map.toString());
```

##### 方式二：
``` java
public class Java8future {

    public static void main(String[] args) {
        Map<String, Integer> map = ImmutableMap.of("gq", 7, "aa", 9, "zs", 66, "vv", 3);
        System.out.println("原始的map：" + map);
        System.out.println("key降序：" + sortByKey(map, true, 2));
        System.out.println("key升序：" + sortByKey(map, false, 2));
        System.out.println("value降序：" + sortByValue(map, true, 2));
        System.out.println("value升序：" + sortByValue(map, false, 2));
    }

    /**
     * Sort map by value
     *
     * @param map map source
     * @param isDesc 是否降序，true：降序，false：升序
     * @param limit 取前几条
     * @return 已排序map
     */
    public static <K, V extends Comparable<? super V>> Map<K, V> sortByValue(Map<K, V> map, boolean isDesc, int limit) {
        Map<K, V> result = Maps.newLinkedHashMap();
        if (isDesc) {
            map.entrySet().stream().sorted(Map.Entry.<K, V>comparingByValue().reversed()).limit(limit)
                    .forEach(e -> result.put(e.getKey(), e.getValue()));
        } else {
            map.entrySet().stream().sorted(Map.Entry.<K, V>comparingByValue())
                    .forEachOrdered(e -> result.put(e.getKey(), e.getValue()));
        }
        return result;
    }

    /**
     * Sort map by key
     *
     * @param map 待排序的map
     * @param isDesc 是否降序，true：降序，false：升序
     * @param limit 取前几条
     * @return 已排序map
     */
    public static <K extends Comparable<? super K>, V> Map<K, V> sortByKey(Map<K, V> map, boolean isDesc, int limit) {
        Map<K, V> result = Maps.newLinkedHashMap();
        if (isDesc) {
            map.entrySet().stream().sorted(Map.Entry.<K, V>comparingByKey().reversed()).limit(limit)
                    .forEachOrdered(e -> result.put(e.getKey(), e.getValue()));
        } else {
            map.entrySet().stream().sorted(Map.Entry.<K, V>comparingByKey())
                    .forEachOrdered(e -> result.put(e.getKey(), e.getValue()));
        }
        return result;
    }
}
```
