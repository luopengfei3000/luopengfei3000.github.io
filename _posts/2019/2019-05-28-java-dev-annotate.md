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