---
layout: post
title: mybatis动态sql学习记录
no-post-nav: true
category: front-end
tags: [front-end]
keywords: front-end
excerpt : mybatis动态sql学习记录
---

## mybatis动态sql学习记录

### 1.多条件查询

```
<select id="getUserList" parameterType="java.util.Map" resultMap="userList">
    SELECT u.* FROM user u
    WHERE u.userName LIKE CONCAT('%','#{userName}','%')
    AND u.userRole=#{userRole}
</select>
```

### 2.if-where的用法

```
<select id="getUserList" parameterType="java.util.Map" resultMap="userList">
    SELECT * FROM user
    <where>
        <if test="userName!=null AND userName!= ''">
            AND user_name LIKE CONCAT('%','#{userName}','%')
        </if>
        <if test="userRole!=null">
            AND user_role=#{userRole}
        </if>
    </where>
</select>
```
**<where>会自动去掉第一个and。**

### 3.if-trim

```
<select id="getUserList" parameterType="java.util.Map" resultMap="userList">
     SELECT * FROM user
     <trim prefix="where" prefixOverrides="and|or">
         <if test="userName!=null and userName!=">
             AND user_code LIKE CONCAT('%','#{userName}','%')
         </if>
         <if test="userRole!=null">
             AND user_role=#{userRole}
         </if>
     </trim>
</select>
```

trim标签的prefixOverrides和prefix分两步骤：

1. 如果prefixOverrides有元素，拿元素去匹配 第一个子标签sql语句，若匹配上，就删掉sql语句的匹配部分。

2. 如果prefix有值，就在第一个子标签sql语句的最前面加上prefix的值。

### 4.if-set 

```
<!--parameterType:属性名，如果是select就写resultMap，是其他的写对应实体对应的路径位置-->
<update id="modifyXxx" parameterType="xxx.xxx.xxx.User">
    UPDATE user
    <set>
        <if test="userCode!=NULL">
            user_code=#{userCode},
        </if>
        <if test="userName!=null">
            user_name=#{userName},
        </if>
        <if test="phone!=null">
            phone=#{phone},
        </if>
    </set>
    WHERE id=#{id}
</update>
```

update user set user_code='xx',user_name='xx',phone='xx'

假设没有涉及到的或者不需要更新的就可以不用更新，set标签可以自动剔除最后一个逗号,

### 5.if-set中的trim

```

<update id="modify" parameterType="xxx.xxx.xxx.User">
    UPDATE user
    <trim prefix="set" suffixOverrides="," suffix="where id=#{id}">
        <if test="userCode!=null">
            user_code=#{userCode},
        </if>
        <if test="userName!=null">
            user_name=#{userName},
        </if>
        <if test="phone!=null">
            phone=#{phone},
        </if>
    </trim>
</update>
```

update user set user_code='xx',user_name='xx',phone='xx' where id='x'

suffixOverrides=","去掉最后一个,

suffix="where id=#{id}"在trim内容的后面加上后缀

### 6.foreach迭代collection数组类型的入参：对于sql语句中含有in语句，则需要foreach标签来实现sql条件的迭代

```
<select id="getUserByRoleId_foreach_array" resultMap="userMapByRole">
    SELECT * FROM user WHERE user_role IN
    <foreach collection="array" item="roleIds" open="(" separator="," close=")">
        #{roleIds}
    </foreach>
</select>
```

eg：SELECT u.* from user u WHERE user_role in(2,4)

-->Dao层接口方法为：List<User> getUserByRoleId_foreach_array(Integer[] roleIds)

-->item :集合中进行迭代时的别名，

-->index :指定一个名称，表示在迭代过程中每次迭代到的位置

-->separator:每次进行迭代用什么分隔符号，in条件语句用逗号（，）为分隔符

-->open：表示该语句以什么开始的，in语句以 “（”开始

-->close：表示该语句以什么符号结束 ，in语句以“）”结束

-->collection：如果是入参类型是参数是List，则collection属性值为list；是一个数组，则为array，如果为多参数，则需要封装成一个Map进行处理

###　７.foreach迭代list类型的入参

Dao层接口方法为:LIst<User> getUserByRoleId_foreach_list(List<Integer> roleList);

```
<select id="getUserByRoleId_foreach_list" resultMap="userMapByRole">
    SELECT * FROM user WHERE user_role IN
    <foreach collection="list" item="roleIds" open="(" separator="," close=")">
        #{roleIds}
    </foreach>
</select>
```

### 8.foreach迭代Map类型的入参

接口方法：public List<User> getUserByRoleId_foreach_map(Map<String,Object>  conditionMap);

java：
```
@Test
public void getUserListByUserName() throws Exception {
    Map<String,Object> conditionMap=new HashMap<String, Object>();
    List<Integer> roleList=new ArrayList<Integer>();
    roleList.add(2);
    //gender是一个限定条件
    conditionMap.put("gender",1);
    //roleIds 对应collection
    conditionMap.put("roleIds",roleList);
    System.out.println("----------------------------------");
    System.out.println(userDao.getUserByRoleId_foreach_map(conditionMap));
    System.out.println("-------------------------------------");
```

mapper:
```
<select id="getUserByRoleId_foreach_map" resultMap="userMapByRole">
    SELECT * FROM user WHERE gender=#{gender} and user_role in
    <foreach collection="roleIds" item="m" open="(" separator="," close=")">
        #{m}
    </foreach>
</select>
```

### 9.choose（when-otherwise）
接口方法：public List<User> getUserList_choose(@Param("userName") String userName, @Param("userRole")Integer userRole,
                                     @Param("userCode")String userCode, @Param("creationDate")Date creationDate);
测试类：
```
@Test
    public void getUserList_choose() throws Exception {
        List<User> userList =new ArrayList<>();
        String userName="张明";
        Integer userRole=2;
        String userCode="";
        Date creationDate=new SimpleDateFormat("yyy-MM-dd").parse("2030-10-26");
        userList=userDao.getUserList_choose(userName,userRole,userCode,creationDate);
        System.out.println(userList);
```
mapper：
```
<select id="getUserList_choose" resultMap="userMapByRole">
    SELECT * FROM user WHERE 1=1
    <choose>
        <when test="userName!=null and userName!=''">
            AND user_name LIKE CONCAT('%',#{userName},'%')
        </when>
        <when test="userCode!=null and userCode!=''">
            AND user_code LIKE CONCAT('%',#{userCode},'%')
        </when>
        <when test="userRole!=null and userRole!=''">
            AND user_role=#{userRole}
        </when>
        <otherwise>
            AND YEAR(creationDate)=YEAR(#{creationDate})
        </otherwise>
    </choose>
</select>
```
-->when：当满足一个条件时跳出循环，

-->otherwise：当所有的when都不满足的时候，执行otherwise

-->choose：相当于switch

-->where 1=1:可以不需要处理多余的and



