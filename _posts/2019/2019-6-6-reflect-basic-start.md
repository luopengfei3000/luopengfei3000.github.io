---
layout: post
title: Java反射篇学习
no-post-nav: true
category: java
tags: [java]
keywords: java
excerpt : Java反射篇学习
---

## Java反射篇学习

先初步理解一下反射的作用：
1. 反编译，将 .class文件转化为 .java文件
2. 通过class对象来访问其对应类的对象的属性、方法、构造方法。

### 1、Class类
先来理解一下Class这个特殊的的类，在平时敲代码的时候，偶尔会写到，this.getClass( )这样的方法，但是却不知道是什么意思。查一下API可知，Class类也同样继承了Object类，Class类的实例用来表示正在运行的java应用程序中的类和接口，也就是说jvm中已经存在对象的类、已经被加载进去的类，都会有对应的Class实例（这也包括基本的数据结构）。

**注意点：**
- Class类保存的是对应类的信息，比如现在有一个Student类，现在通过new 方法 产生了一个stu1的实例，那么jvm会把Stduent类加载进内存，通过类加载器中等defineClass方法构造 一个对应的Class对象，来保存Student的一些信息，包括其中的一些方法、属性、构造方法等。
- 同一种类型的对象或者数据类型，共享一个class实例。比如说，Student方法继续产生stu2、stu3.。。。等等的对象，即使再多，也只有一个对应的Class实例来保存他们的信息。
- 通过Class实例，可以反向地获取对应类的信息，如果指定对象（例如 stu1), 可以反向获得他的属性、方法等信息。

### 2、反射的使用
#### 获取Class对象的方法 
``` java
 /**
   * 获取class对象的三种方式
   * 1、object ——> getClass();
   * 2、任何数据类型（包括基本数据类型）都有一个静态的class属性。
   * 3、通过class类的静态方法：forName（String className）（常用）
   *
   */
 public class Demo_1 {
     public static void main(String[] args) {
         //第一种获得class对象的方法
         Student stu1 = new Student();//这一个new产生一个Student对象,一个Class对象
         Class stuClass = stu1.getClass();//获取class对象
         System.out.println(stuClass.getName());
         
         //第二种方式获取class对象
         Class stuClass2 = Student.class;
         System.out.println(stuClass == stuClass2);
         
         //第三种方式获取class对象
         try {
             Class stuClass3 = Class.forName("fanshe.Student");//带包名
             System.out.println(stuClass3 == stuClass2);
         } catch (ClassNotFoundException e) {
             e.printStackTrace();
         }
     }
 }
```
Student：
``` java
class Student{
    Student(){}
}
```
结果：
```
fanshe.Student
true
true
```

总结：第三种方法比较常见，不用导包，也不用知道对象。上面的测试也说明了，同一个对象共享一个Class实例，一个类只产生一个class实例。

#### 通过反射获取构造函数
Student类：
``` java
public class Student {
    //默认构造方法
    Student(String str){
        System.out.println("(默认)的默认构造方法 s = " + str);
    }
    
    //无参构造方法
    public Student() {
        System.out.println("调用了共有的、无参构造方法执行");
    }
    
    //有一个参数的构造方法
    public Student(char name) {
        System.out.println("姓名： " + name);
    }
    
    //有多个参数的构造方法
    public Student(String name, int age) {
        System.out.println("姓名： " + name+ "年龄：" +age);//这的执行效率有问题
    }
    
    //受保护的构造方法
    protected Student(boolean n) {
        System.out.println("受保护的构造方法 n= " + n);
    }
    
    //私有构造方法
    private Student(int age) {
        System.out.println("私有构造方法  年龄：" + age);
    }
}
```

测试类：
``` java
import java.lang.reflect.Constructor;
/**
 * 
 * 通过Class对象可以获取某个类中的构造方法、成员变量、成员方法：并访问成员;
 * 1、获取构造方法：
*        （1）批量的方法
 *             public Constructor[] getConstructor(); 所有“公有的”构造方法
 *             public Constructor[] getDeclaredConstructors(); 获取所有的构造方法
 *             
 *               （2）获取单个的方法，并调用：
 *             public Constructor getConstructor(Class parameterTypes):获取单个公有的构造方法
 *             public Constructor getDeclaredConstructor(Class parameterTypes);获取某个构造方法可以是私有的，或者是受保护的、默认的。公有的：
 *             调用构造方法
 *             Construtor -->newInstance(Object、、、initargs)
 *             
 *
 */
public class Test_1 {
    public static void main(String[] args)throws Exception{
        //1、加载class对象
        Class clazz = Class.forName("fanshe.Student");
        
        //2、获取所有公有构造方法
        System.out.println("*****************************所有公有构造方法*************");
        Constructor[] conArray = clazz.getConstructors();
        for(Constructor c : conArray) {
            System.out.println(c);
        }
        
        System.out.println("********所有的构造方法（包括私有、受保护、默认、公有）********");
        conArray = clazz.getDeclaredConstructors();
        for(Constructor c : conArray) {
            System.out.println(c);
        }
        
        System.out.println("********获取公有的 、无参的构造方法");
        Constructor con = clazz.getConstructor(null);
         //1>、因为是无参的构造方法所以类型是一个null,不写也可以：这里需要的是一个参数的类型，切记是类型  
         //2>、返回的是描述这个无参构造函数的类对象。  
        
        System.out.println("con = "+con);
        
        //调用构造方法
        Object obj = con.newInstance();
        
        
        System.out.println("******************获取私有构造方法，并调用*******************************"); 
        con = clazz.getDeclaredConstructor(char.class);
        System.out.println(con);
        
        //调用构造函数
        con.setAccessible(true);//暴力访问（忽略掉访问修饰符）
        obj = con.newInstance('男');        
    }    
}
```
结果：
```
*****************************所有公有构造方法*************
public fanshe.Student(java.lang.String,int)
public fanshe.Student(char)
public fanshe.Student()
********所有的构造方法（包括私有、受保护、默认、公有）********
private fanshe.Student(int)
protected fanshe.Student(boolean)
public fanshe.Student(java.lang.String,int)
public fanshe.Student(char)
public fanshe.Student()
fanshe.Student(java.lang.String)
********获取公有的 、无参的构造方法
con = public fanshe.Student()
调用了共有的、无参构造方法执行
******************获取私有构造方法，并调用*******************************
public fanshe.Student(char)
姓名： 男
```
#### 获取成员变量并调用
Stduent类：
``` java
public class Student_1 {

    public Student_1() {
    }
    //******字段******//
    public String name;
    protected int age;
    char sex;
    private String phoneNum;
    
    public String toString() {
        return "Student [name="+name+",age="+age+",sex="+sex+", phoneNum="+phoneNum+"]";
    }
}
```

测试类：
``` java
/**
 * 
 * 获取成员变量并调用： 
 *  
 * 1.批量的 
 *      1).Field[] getFields():获取所有的"公有字段" 
 *      2).Field[] getDeclaredFields():获取所有字段，包括：私有、受保护、默认、公有； 
 * 2.获取单个的： 
 *      1).public Field getField(String fieldName):获取某个"公有的"字段； 
 *      2).public Field getDeclaredField(String fieldName):获取某个字段(可以是私有的) 
 *  
 *   设置字段的值： 
 *      Field --> public void set(Object obj,Object value): 
 *                  参数说明： 
 *                  1.obj:要设置的字段所在的对象； 
 *                  2.value:要为字段设置的值； 
 */
public class Test_2 {

    public static void main(String[] args) throws Exception {
        //1.获取Class对象  
        Class stuClass = Class.forName("fanshe.Student_1");  
        //2.获取字段  
        System.out.println("************获取所有公有的字段********************");  
        Field[] fieldArray = stuClass.getFields();  
        for(Field f : fieldArray){  
            System.out.println(f);  
        }  
        System.out.println("************获取所有的字段(包括私有、受保护、默认的)********************");  
        fieldArray = stuClass.getDeclaredFields();  
        for(Field f : fieldArray){  
            System.out.println(f);  
        }  
        System.out.println("*************获取公有字段**并调用***********************************");  
        Field f = stuClass.getField("name");  
        System.out.println(f);  
        //获取一个对象  
        Object obj = stuClass.getConstructor().newInstance();//产生Student对象--》Student stu = new Student();  
        //为字段设置值  
        f.set(obj, "刘德华");//为Student对象中的name属性赋值--》stu.name = "刘德华"  
        //验证  
        Student_1 stu = (Student_1)obj;  
        System.out.println("验证姓名：" + stu.name);  
          
          
        System.out.println("**************获取私有字段****并调用********************************");  
        f = stuClass.getDeclaredField("phoneNum");  
        System.out.println(f);  
        f.setAccessible(true);//暴力反射，解除私有限定  
        f.set(obj, "18888889999");  
        System.out.println("验证电话：" + stu); 
    }
}
```

结果：
```
************获取所有公有的字段********************
public java.lang.String fanshe.Student_1.name
************获取所有的字段(包括私有、受保护、默认的)********************
public java.lang.String fanshe.Student_1.name
protected int fanshe.Student_1.age
char fanshe.Student_1.sex
private java.lang.String fanshe.Student_1.phoneNum
*************获取公有字段**并调用***********************************
public java.lang.String fanshe.Student_1.name
验证姓名：刘德华
**************获取私有字段****并调用********************************
private java.lang.String fanshe.Student_1.phoneNum
验证电话：Student [name=刘德华,age=0,sex=
```
#### 获取成员方法并调用
Stduent类
``` java
public class Student_3 {

    //**************成员方法***************//  
    public void show1(String s){  
        System.out.println("调用了：公有的，String参数的show1(): s = " + s);  
    }  
    protected void show2(){  
        System.out.println("调用了：受保护的，无参的show2()");  
    }  
    void show3(){  
        System.out.println("调用了：默认的，无参的show3()");  
    }  
    private String show4(int age){  
        System.out.println("调用了，私有的，并且有返回值的，int参数的show4(): age = " + age);  
        return "abcd";  
    }  
}
```
测试类：
``` java
import java.lang.reflect.Method;

/**
* 获取成员方法并调用： 
 *  
 * 1.批量的： 
 *      public Method[] getMethods():获取所有"公有方法"；（包含了父类的方法也包含Object类） 
 *      public Method[] getDeclaredMethods():获取所有的成员方法，包括私有的(不包括继承的) 
 * 2.获取单个的： 
 *      public Method getMethod(String name,Class<?>... parameterTypes): 
 *                  参数： 
 *                      name : 方法名； 
 *                      Class ... : 形参的Class类型对象 
 *      public Method getDeclaredMethod(String name,Class<?>... parameterTypes) 
 *  
 *   调用方法： 
 *      Method --> public Object invoke(Object obj,Object... args): 
 *                  参数说明： 
 *                  obj : 要调用方法的对象； 
 *                  args:调用方式时所传递的实参； 
 *
 */

public class Test_3 {

    public static void main(String[]args) throws Exception{
         //1.获取Class对象  
        Class stuClass = Class.forName("fanshe.Student_3");  
        //2.获取所有公有方法  
        System.out.println("***************获取所有的”公有“方法*******************");  
        stuClass.getMethods();  
        Method[] methodArray = stuClass.getMethods();  
        for(Method m : methodArray){  
            System.out.println(m);  
        }  
        System.out.println("***************获取所有的方法，包括私有的*******************");  
        methodArray = stuClass.getDeclaredMethods();  
        for(Method m : methodArray){  
            System.out.println(m);  
        }  
        System.out.println("***************获取公有的show1()方法*******************");  
        Method m = stuClass.getMethod("show1", String.class);  
        System.out.println(m);  
        //实例化一个Student对象  
        Object obj = stuClass.getConstructor().newInstance();  
        m.invoke(obj, "刘德华");  
          
        System.out.println("***************获取私有的show4()方法******************");  
        m = stuClass.getDeclaredMethod("show4", int.class);  
        System.out.println(m);  
        m.setAccessible(true);//解除私有限定  
        Object result = m.invoke(obj, 20);//需要两个参数，一个是要调用的对象（获取有反射），一个是实参  
        System.out.println("返回值：" + result);  
    }
}
```
结果：
```
***************获取所有的”公有“方法*******************
public void fanshe.Student_3.show1(java.lang.String)
public final void java.lang.Object.wait() throws java.lang.InterruptedException
public final void java.lang.Object.wait(long,int) throws java.lang.InterruptedException
public final native void java.lang.Object.wait(long) throws java.lang.InterruptedException
public boolean java.lang.Object.equals(java.lang.Object)
public java.lang.String java.lang.Object.toString()
public native int java.lang.Object.hashCode()
public final native java.lang.Class java.lang.Object.getClass()
public final native void java.lang.Object.notify()
public final native void java.lang.Object.notifyAll()
***************获取所有的方法，包括私有的*******************
protected void fanshe.Student_3.show2()
private java.lang.String fanshe.Student_3.show4(int)
void fanshe.Student_3.show3()
public void fanshe.Student_3.show1(java.lang.String)
***************获取公有的show1()方法*******************
public void fanshe.Student_3.show1(java.lang.String)
调用了：公有的，String参数的show1(): s = 刘德华
***************获取私有的show4()方法******************
private java.lang.String fanshe.Student_3.show4(int)
调用了，私有的，并且有返回值的，int参数的show4(): age = 20
返回值：abcd
```
#### 反射main方法
student类：
``` java
public class Student_4 {

    public static void main(String[]args) {
        System.out.println("main 方法执行了。。。");
    }
}
```
测试类：
``` java
import java.lang.reflect.Method;
/** 
 * 获取Student类的main方法、不要与当前的main方法搞混了 
 */  
public class Test_4 {

    public static void main(String[]args) {
        try {  
            //1、获取Student对象的字节码  
            Class clazz = Class.forName("fanshe.Student_4");  
              
            //2、获取main方法  
             Method methodMain = clazz.getMethod("main", String[].class);//第一个参数：方法名称，第二个参数：方法形参的类型，  
            //3、调用main方法  
            // methodMain.invoke(null, new String[]{"a","b","c"});  
             //第一个参数，对象类型，因为方法是static静态的，所以为null可以，第二个参数是String数组，这里要注意在jdk1.4时是数组，jdk1.5之后是可变参数  
             //这里拆的时候将  new String[]{"a","b","c"} 拆成3个对象。。。所以需要将它强转。  
             methodMain.invoke(null, (Object)new String[]{"a","b","c"});//方式一  
            // methodMain.invoke(null, new Object[]{new String[]{"a","b","c"}});//方式二  
              
        } catch (Exception e) {  
            e.printStackTrace();  
        }       
    }
}
```

结果：
```
main 方法执行了。。。
```

#### 通过反射运行配置文件
student类：
``` java
public class Student {  
     public void show(){  
         System.out.println("is show()");  
     }  
 } 
```

配置文件，以txt为例（pro.txt）
```
1 className = cn.fanshe.Student  
2 methodName = show 
```

测试类：
``` java
import java.io.FileNotFoundException;  
import java.io.FileReader;  
import java.io.IOException;  
import java.lang.reflect.Method;  
import java.util.Properties;  
  
/* 
 * 我们利用反射和配置文件，可以使：应用程序更新时，对源码无需进行任何修改 
 * 我们只需要将新类发送给客户端，并修改配置文件即可 
 */  
public class Demo {  
    public static void main(String[] args) throws Exception {  
        //通过反射获取Class对象  
        Class stuClass = Class.forName(getValue("className"));//"cn.fanshe.Student"  
        //2获取show()方法  
        Method m = stuClass.getMethod(getValue("methodName"));//show  
        //3.调用show()方法  
        m.invoke(stuClass.getConstructor().newInstance());  
          
    }  
      
    //此方法接收一个key，在配置文件中获取相应的value  
    public static String getValue(String key) throws IOException{  
        Properties pro = new Properties();//获取配置文件的对象  
        FileReader in = new FileReader("pro.txt");//获取输入流  
        pro.load(in);//将流加载到配置文件对象中  
        in.close();  
        return pro.getProperty(key);//返回根据key获取的value值  
    }  
}  
```

结果：
```
is show()
```

需求：更新系统时，只要再写一个Student2类，更改一下配置文件，而不需要动原来的类。

Student2类：
``` java
public class Student2 {  
     public void show2(){  
         System.out.println("is show2()");  
     }  
 } 
```
配置文件：
```
className = cn.fanshe.Student2  
methodName = show2 
```
#### 通过反射越过泛型检查
测试类：
``` java
import java.lang.reflect.Method;  
import java.util.ArrayList;  
  
/* 
 * 通过反射越过泛型检查 
 *  
 * 例如：有一个String泛型的集合，怎样能向这个集合中添加一个Integer类型的值？ 
 */  
public class Demo {  
    public static void main(String[] args) throws Exception{  
        ArrayList<String> strList = new ArrayList<>();  
        strList.add("aaa");  
        strList.add("bbb");  
          
    //  strList.add(100);  
        //获取ArrayList的Class对象，反向的调用add()方法，添加数据  
        Class listClass = strList.getClass(); //得到 strList 对象的字节码 对象  
        //获取add()方法  
        Method m = listClass.getMethod("add", Object.class);  
        //调用add()方法  
        m.invoke(strList, 100);  
          
        //遍历集合  
        for(Object obj : strList){  
            System.out.println(obj);  
        }  
    }  
}
```
输出：
```
aaa
bbb
100
```
### 3、反射实战

Java反射机制调用对象的方法：将一个对象的属性值赋值给另一个对象的属性

Person1:
``` java
public class Person1 {
	private String id;
	private String name;
	private String password;
    //get/set/toString...
}
```

Person2:
``` java
public class Person1 {
	private String id;
	private String name;
    //get/set/toString...
}
```
BaseUtil:
``` java
public class BaseUtil {

    /**
     * 将一个集合中对象的值拷贝到另一个对象，属性相同即赋值
     * 
     * @param source 源数据，将此对象数据取出
     * @param tClass 目标对象，将取出的数据赋值到该对象中
     * @param <T> 源数据类型
     * @param <E> 目标数据类型
     * @return 被赋值后的目标对象集合
     * @throws Exception 通过反射创建对象或调用方法时抛出的异常
     */
    public static <T, E> List<E> copyProperties(List<T> source, Class<E> tClass) throws Exception {
        // 判断传入源数据是否为空，如果空，则抛自定义异常
        if (null == source) {
            throw new Exception("数据源为空");
        }
        // 创建一个集合，用于存储目标对象，全部数据抓换完成后，将该集合返回
        List<E> targetList = Lists.newArrayList();
        // 循环取到单个源对象
        for (T t : source) {
            // 获取源对象的类的详情信息
            Class<?> sClass = t.getClass();
            // 获取源对象的所有属性
            Field[] sFields = sClass.getDeclaredFields();
            // 获取目标对象的所有属性
            Field[] tFields = tClass.getDeclaredFields();
            E target;
            try {
                // 通过类的详情信息，创建目标对象 这一步等同于UserTwo target = new UserTwo()；
                target = tClass.newInstance();
            } catch (Exception e) {
                e.printStackTrace();
                throw new Exception("目标对象创建失败");
            }
            // 循环取到源对象的单个属性
            for (Field sField : sFields) {
                // 循环取到目标对象的单个属性
                for (Field tField : tFields) {
                    // 判断源对象的属性名、属性类型是否和目标对象的属性名、属性类型一致
                    if (sField.getName().equals(tField.getName())
                        && sField.getGenericType().equals(tField.getGenericType())) {
                        try {
                            // 获取源对象的属性名，将属性名首字母大写，拼接如：getUsername、getId的字符串
                            String sName = sField.getName();
                            char[] sChars = sName.toCharArray();
                            sChars[0] -= 32;
                            String sMethodName = "get" + String.valueOf(sChars);
                            // 获得属性的get方法
                            Method sMethod = sClass.getMethod(sMethodName);
                            // 调用get方法
                            Object sFieldValue = sMethod.invoke(t);
                            // 获取目标对象的属性名，将属性名首字母大写，拼接如：setUsername、setId的字符串
                            String tName = tField.getName();
                            char[] tChars = tName.toCharArray();
                            tChars[0] -= 32;
                            String tMethodName = "set" + String.valueOf(tChars);
                            // 获得属性的set方法
                            Method tMethod = tClass.getMethod(tMethodName, tField.getType());
                            // 调用方法，并将源对象get方法返回值作为参数传入
                            tMethod.invoke(target, sFieldValue);
                            break;
                        } catch (Exception e) {
                            e.printStackTrace();
                            throw new Exception("转换失败，请检查属性类型是否匹配");
                        }
                    }
                }
            }
            // 将通过反射创建的目标对象放入集合中
            targetList.add(target);
        }
        // 返回集合
        return targetList;
    }
}
```
测试：
``` java
public class Test {
	public static void main(String[] args) throws Exception {
		List<Person1> list1 = new ArrayList<Person1>();

		Person1 p1 = new Person1();
		p1.setId("111");
		p1.setName("111");
		p1.setPassword("111");

		list1.add(p1);

		List<Person2> list2 = new ArrayList<Person2>();

		List<Person2> person2s = BaseUtil.copyProperties(list1, Person2.class);
		
		System.out.println(person2s);
	}
}
```

结果：
```
[Person2{id='111', name='111'}]
```
