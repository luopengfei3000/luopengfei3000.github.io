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
        
        public static List<Student> generate() {
            List<Student> list = new ArrayList<Student>(10);
            list.add(new Student(12, "aa",88));
            list.add(new Student(17, "cc",99));
            list.add(new Student(14, "bb",85));
            list.add(new Student(12, "ff",52));
            list.add(new Student(16, "ss",92));
            list.add(new Student(13, "kk",74));
            list.add(new Student(18, "nn",53));
            list.add(new Student(20, "rr",91));
            list.add(new Student(19, "ww",79));
            list.add(new Student(21, "pp",86));
            list.add(new Student(34, "pp",44));
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
```
- 使用stream排序

``` java
    List<Student> students = Student.generate();
    按照成绩升序排
    students = students.stream().sorted(Comparator.comparing(Student::getGrade)).collect(Collectors.toList());
    按照成绩降序排
    students = students.stream().sorted(Comparator.comparing(Student::getGrade).reversed()).collect(Collectors.toList());//按照成绩降序
    
    如果类实现Comparable（implements Comparable<Student>） 并且 重写compareTo方法例如:
    @Override
    public int compareTo(Student ob) {
        return name.compareTo(ob.getName());
    }
    此时，下面方法按照name排序
    students = students.stream().sorted().collect(Collectors.toList());
    students.forEach(System.out::println);
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
[Student{age=22, grade=0, name='路飞'}, 
Student{age=40, grade=0, name='红发'}, 
Student{age=50, grade=185, name='白胡子'}, 
Student{age=25, grade=183, name='艾斯'}, 
Student{age=48, grade=176, name='雷利'}]
```

#### Map集合排序

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
