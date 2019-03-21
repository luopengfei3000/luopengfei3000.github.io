---
layout: post
title: markdown 使用总结
category: other
tags: [other]
---

markdown来写文章，有一些命令或者是快捷键不是特别熟悉在此记录下来，方便日后查看


## 常用语法
### 标题
```
public class Person{

    private int age;
    private String name;
    static String address;

	public Person() {
	
	}

	public Person(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public String getName() {
        return name;
    }

	@Override
	public String toString() {
		return "名称："+this.name+"---年龄："+this.age+"---地址："+Person.address;
	}
}
``` 

### Test类

```
public class Test {
    public static void main(String[] args) {
	    Person p1 = new Person();
	    p1.setName("张三");
	    p1.setAge(80);
	    p1.address = "月球";
	    Person p2 = new Person();
	    p2.setName("李四");
	    p2.setAge(20);
	    p2.address = "地球";
	    System.out.println("p1--->"+p1);
	    System.out.println("p2--->"+p2);
    }
}
``` 

### 输出结果

``` xml
p1--->名称：张三---年龄：80---地址：地球
p2--->名称：李四---年龄：20---地址：地球
```
### 内存图

![](https://luopengfei3000.github.io/assets/images/2019/article/2019-03-12-java-memory-diagrams/2019-03-12-java-memory-diagrams.png)

### 执行流程

<p>java文件通过编译成class文件，class文件通过类加载器加载到方法区中，程序首先会加载核心类库，也就是程序运行所需要的一些最基本的类。</p>

<p>接着程序会找代码主方法所在的类，Test.class这个类加载到方法区中，这个类中没有属性，但有一个静态方法main，此时将Test.class
这个类所在内存分成两部分，一部分是非静态区域，一部分是静态区域，main方法加载到静态区域，但并未执行，这一步仅仅是将mian方法存储到方法区，
mian方法是由JVM调用的，JVM调用main方法，mian方法开始在栈中执行，在栈中开辟一块空间。</p>

<p>然后接着按照代码顺序往下执行，接着执行Person p1 = new Person();准备创建Person对象，要创建对象必须先找到对应Person类，
此时发现内存中并没有Person类，然后就开始加载Person类（Person类是在第一次使用的时候才会加载，
比如有多个类，不是全部一起加载的，是当你需要用到哪个类才会加载对应的类)，加载Person类时发现有对应的属性和方法，Person类将空间分成静态和非静态两部分，
将name,age,toString()加载到非静态区域，将address加载到静态区域，由于address是一个静态属性，Java在编译和运行时候会给这个属性一个默认值(String类型默认值为null)
这个过程叫做address属性初始化，此时address属性在方法区中有了具体的大小，也就意味着address属性在方法区占用了一块内存，就会产生对应的内存地址0xa22a，
加载完Person类后发现并没有对象，也就意味着静态变量先于对象而存在，静态变量是随着类的加载而加载到方法区中的，在方法区被赋予默认值(静态变量可以不依附于对象，
没有对象也能使用静态变量，可以用过类名来调用，所以静态变量也称类变量)。</p>

<p>加载完Person类，开始创建p1对象，首先在栈中mian方法区开辟一块空间p1，在堆内存中开辟一块空间，
地址为0x855e，将地址赋值给p1，p1顺着地址可以找到这块空间，而空间里有对应的属性，分别赋上默认值，在给address赋默认值时，发现address是一个静态属性，
然后程序会去方法区的对应类中寻找是否有对应的静态属性，找到之后会把方法区中对应静态属性的地址0xa22a拿过来，然后堆内存中的address属性通过地址找到方法区中address属性。
接着下行代码，开始给属性赋值，name赋值为张三，age赋值为80，给address赋值时，address会根据地址一直找到方法区中的address，将默认值null覆盖，然后赋值为月球。</p>

<p>接着执行代码Person p2 = new Person();同理，先找到对应的Person类，此时发现Person类已经在方法区中加载了，此时就不会再重复加载了，因为类只加载一次，既然Person类已经加载了，
就直接创建p2对象，同理，分别在堆内存和栈中开辟空间，地址赋值，属性赋值，address根据地址找到方法区中的address，经原来的月球，重新赋值为地球。因为静态变量在内存中共享，因此两个类中address都赋值为地球。</p>
