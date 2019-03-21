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

### 引用
在我们写作的时候经常需要引用他人的文字，这个时候引用这个格式就很有必要了，在 Markdown 中，你只需要在你希望引用的文字前面加上```   > ``` 就好了，例如：

> markdown 引用

注：> 和文本之间要保留一个字符的空格。

