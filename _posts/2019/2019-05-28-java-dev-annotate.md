---
layout: post
title: 开发中注解的使用
no-post-nav: true
category: java
tags: [java]
keywords: java
excerpt : 开发中注解的使用
---

## 开发中注解的使用

### @JsonProperty的使用

jackson的maven依赖

```
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.5.3</version>
</dependency>
```

**用法：**
`@JsonProperty` 此注解用于属性上，作用是把该属性的名称序列化为另外一个名称，如把trueName属性序列化为name，@JsonProperty(value="true_name")。

``` java
public class TestDTO {
	@JsonProperty("trst_id")
	private String trstId;

	@JsonProperty("trst_name")
	private String trstName;

	@JsonProperty("trst_code")
	private String trstCode;

    get/set/toString 省略
```

**测试**
``` java
public class Test {
	public static void main(String[] args){
		TestDTO test = new TestDTO();
		test.setTrstId("111");
		test.setTrstName("222");
		test.setTrstCode("333");
		
		//JsonHelper封装了ObjectMapper，使用fasterxml.jackson提供的方法
		//将对象序列化json字符串
		String json = JsonHelper.getInstance().writeValueAsString(test);
		System.out.println(json);

		//将json字符串转成dto
		DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
		TestDTO param = JsonHelper.getInstance().readValue(json, dateFormat, new TypeReference<TestDTO>() {});
		System.out.println(param);
	}
}
```

**结果**
``` java
{"trst_id":"111","trst_name":"222","trst_code":"333"}
TestDTO{trstId='111', trstName='222', trstCode='333'}
```

@JsonProperty不仅仅是在序列化的时候有用，反序列化的时候也有用，比如有些接口返回的是json字符串，命名又不是标准的驼峰形式，在映射成对象的时候，将类的属性上加上@JsonProperty注解，里面写上返回的json串对应的名字

**注意：使用阿里的fastjson序列化不生效，因为fastjson不认识@JsonProperty注解！所以要使用jackson自己的序列化工具方法！**

### @JsonIgnore的使用

1. 作用：在json序列化时将java bean中的一些属性忽略掉，序列化和反序列化都受影响。

2. 使用方法：一般标记在属性或者方法上，返回的json数据即不包含该属性。

``` java
public class TestDTO {
	@JsonProperty("trst_id")
	private String trstId;

	@JsonProperty("trst_name")
	private String trstName;

	@JsonIgnore
	private String trstCode;

    get/set/toString 省略
```
**测试**
``` java
public class Test {
	public static void main(String[] args){
		TestDTO test = new TestDTO();
        test.setTrstId("111");
        test.setTrstName("222");
        test.setTrstCode("333");
        //将对象序列化json字符串
        String json = JsonHelper.getInstance().writeValueAsString(test);
        System.out.println(json);
	}
}
```

**结果**
``` java
{"trst_id":"111","trst_name":"222"}
```

**注意：使用阿里的fastjson序列化不生效，因为fastjson不认识@JsonIgnore注解！所以要使用jackson自己的序列化工具方法！**

### @Component, @Repository, @Service的区别

引用spring的官方文档中的一段描述：

>在Spring2.0之前的版本中，@Repository注解可以标记在任何的类上，用来表明该类是用来执行与数据库相关的操作（即dao对象），并支持自动处理数据库操作产生的异常
<br/>在Spring2.5版本中，引入了更多的Spring类注解：@Component,@Service,@Controller。@Component是一个通用的Spring容器管理的单例bean组件。而@Repository, @Service, @Controller就是针对不同的使用场景所采取的特定功能化的注解组件。
<br/>因此，当你的一个类被@Component所注解，那么就意味着同样可以用@Repository, @Service, @Controller来替代它，同时这些注解会具备有更多的功能，而且功能各异。
<br/>最后，如果你不知道要在项目的业务层采用@Service还是@Component注解。那么，@Service是一个更好的选择。
<br/>就如上文所说的，@Repository早已被支持了在你的持久层作为一个标记可以去自动处理数据库操作产生的异常（译者注：因为原生的java操作数据库所产生的异常只定义了几种，但是产生数据库异常的原因却有很多种，这样对于数据库操作的报错排查造成了一定的影响；而Spring拓展了原生的持久层异常，针对不同的产生原因有了更多的异常进行描述。所以，在注解了@Repository的类上如果数据库操作中抛出了异常，就能对其进行处理，转而抛出的是翻译后的spring专属数据库异常，方便我们对异常进行排查处理）。

  **注解** | **含义**
  :-------------: | :-------------: 
 @Component  | 最普通的组件，可以被注入到spring容器进行管理
 @Repository  | 作用于持久层
 @Service  | 作用于业务逻辑层
 @Controller  | 作用于表现层(spring-mvc的注解)