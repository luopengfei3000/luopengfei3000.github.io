---
layout: post
title: Java开发问题汇总
no-post-nav: true
category: java
tags: [java]
keywords: java
excerpt : Java开发问题汇总
---

### Java开发问题汇总

#### 1.集合和数组之间的相互转换
数组变集合：

通常我们会回答的是以下代码：
```
List<String> list = Arrays.asList(array);
```

但这并不是很好的答案，此时组合成的list是Arrays里面的一个静态内部类，该类并未实现add、remove方法，因此在使用时存在问题。

可以这样：
```
String array[] = {"hello","world","java","zhiyin"};
List<String> list = new ArrayList<String>(Arrays.asList(array));
```

集合变数组：
```
String[] array = list.toArray(new String[list.size()]);
```

Arrays.asList() 创建的 List 对象是通过实例化 Arrays 内部类 ArrayList 来创建的，所以这个 ArrayList 并不是我们常用的那个 ArrayList。

**1、该方法返回的是一个固定长度的列表**

所以它的长度是不能被改变的，也就不能对它进行添加和删除元素的操作，从它的内部类ArrayList的方法列表也可以看出，并没有覆盖add和remove方法，因此对这两个方法的调用都会导致抛出异常。

虽然不能改变列表的长度，但是可以改变列表中的元素，以及元素的位置。比如通过set方法来重新设值，通过replaceAll方法来批量替换，通过sort方法来排序等等。

**2、任何对列表的改动都会回写到原来是数组**

也就是说对返回的列表进行的任何修改操作，都会导致原数组的改变。

可以通过一个Test来测试一下：
```
public void testArrays() {
    Long[] longs = {1L,2L,4L,3L};
    List<Long> longList = Arrays.asList(longs);
    System.out.println("longList:" + JSON.toJSONString(longList) + "longs:" + JSON.toJSONString(longs));

    longList.set(1, 5L);
    System.out.println("longList:" + JSON.toJSONString(longList) + "longs:" + JSON.toJSONString(longs));

    longList.replaceAll(a -> a + 1L);
    System.out.println("longList:" + JSON.toJSONString(longList) + "longs:" + JSON.toJSONString(longs));

    longList.sort(Long::compareTo);
    System.out.println("longList:" + JSON.toJSONString(longList) + "longs:" + JSON.toJSONString(longs));

    longs[2] = 7L;
    System.out.println("longList:" + JSON.toJSONString(longList) + "longs:" + JSON.toJSONString(longs));
}
```
输出结果:
```
longList:[1,2,4,3]longs:[1,2,4,3]
longList:[1,5,4,3]longs:[1,5,4,3]
longList:[2,6,5,4]longs:[2,6,5,4]
longList:[2,4,5,6]longs:[2,4,5,6]
longList:[2,4,7,6]longs:[2,4,7,6]
```
注意：不能使用基本数据类型数组来作为参数

```
public void testArrays2() {
    int[] ints = { 1, 2, 3 };
    List list = Arrays.asList(ints);
    System.out.println(list.size());//结果为1
}
```
参数的类型T指的是数组中的元素类型，如果数组中元素类型是基本类型，就会把整个数组当成一个元素

#### 2.Mybatis中动态传入状态值的参数

例如：WORKFLOW表 中有字段status(状态^1.未提交，2审批中，3审批通过，4拒绝)

方式一：页面传值
```
<form id="form">
    <table>
        <tr>
            <input type="hidden" id="longStatus" name="longStatus" value="1,2"/>
        </tr>
    </table>
</form>
```
mybatis中接收数据：
```
<if test="status != null and status != ''">
    and instr(',' || #{status} , ',' || t1.STATUS, 1, 1) != 0
</if>
```
结果：当页面传入"1,2"时,查询数据库中status为1和2的值，当传入"1,2,3"时，查询数据库中status为1和2和3的值，and so on

方式二：页面传值
```
var param = {
	status : "1', '2"
}
_self._datagrid = $(_self._datagridId).datagrid({
    queryParams: {param: JSON.stringify(param)},
    url:this.getUrl()+"getDataPage"
});
```
mybatis中接收数据：
```
<if test="status != null and status != ''">
    and t1.STATUS in ('${status}')
</if>
```
结果：当页面传入"1,2"时,查询数据库中status为1和2的值，当传入"1,2,3"时，查询数据库中status为1和2和3的值，and so on

#### 3.后端传Long类型至前端js会出现精度丢失问题

Java后端的Long类型数据，传到前端会出现精度丢失，如：164379764419858435，前端会变成164379764419858430。在浏览器中做测试可知，这就是一个精度丢失的问题。

解决思路是：后台传到前台时，Long类型数据，转为String类型。
1. 可以直接操作传回的对象数据，toString()该long类型数据。
2. 如果使用Jackson注解，我们也可以用@JsonFormat做类型转换（注意哦，这个不管可以使用在format日期类型哦），使用方法如下：

添加如下注解：
```
@JsonFormat(shape=JsonFormat.Shape.STRING)
```
```
import com.fasterxml.jackson.annotation.JsonFormat;
@JsonFormat(shape=JsonFormat.Shape.STRING)
@FieldRemark(column = "g_no", field = "gNo", name = "序号")
private Long gNo;
```

#### 4.将sql传入mapper中查询改进

在开发过程中看到如下代码：
``` java
//Service:
public List<Map<String, Object>> getSysDeptList(String deptId, String type) {
    String sql = "";
    if ("init".equals(type)) {
        sql = "SELECT a.id,a.dept_name FROM sys_dept_v a\n" + "WHERE EXISTS (\n" + "SELECT 1\n" + "FROM Sys_Dept t\n" + "WHERE t.parent_dept_id = '-1'\n" + "AND\n"
                + "a.parent_dept_id = t.id\n" + ")\n" + "START WITH a.id = '" + deptId + "'\n" + "CONNECT BY PRIOR a.parent_dept_id = a.id";

    } else {
        sql = "SELECT a.id,a.dept_name,(SELECT COUNT(ID) FROM sys_dept b WHERE b.parent_dept_id = a.id) AS c_sub FROM sys_dept_v a where a.parent_dept_id = '" + deptId
                + "' ORDER BY a.order_by";
    }
    return dao.getDataBySql(sql);
}
//Mapper:
<!-- 查询 SQL -->
<select id="getDataBySql" parameterType="java.util.Map" resultType="java.util.Map">
    ${sql}
</select>
```
通过JdbcTemplate实现：
``` java
@Autowired
private JdbcTemplate jdbcTemplate;

public List<Map<String, Object>> getSysDeptList(String deptId, String type) {
		String sql = "";
		if ("init".equals(type)) {
			sql = "SELECT a.id,a.dept_name FROM sys_dept_v a\n" + "WHERE EXISTS (\n" + "SELECT 1\n" + "FROM Sys_Dept t\n" + "WHERE t.parent_dept_id = '-1'\n" + "AND\n"
					+ "a.parent_dept_id = t.id\n" + ")\n" + "START WITH a.id = '" + deptId + "'\n" + "CONNECT BY PRIOR a.parent_dept_id = a.id";

		} else {
			sql = "SELECT a.id,a.dept_name,(SELECT COUNT(ID) FROM sys_dept b WHERE b.parent_dept_id = a.id) AS c_sub FROM sys_dept_v a where a.parent_dept_id = '" + deptId
					+ "' ORDER BY a.order_by";
		}
		List<Map<String, Object>> list = jdbcTemplate.queryForList(sql);
		return list;
	}
```
#### 5.Oracle DBLINK 简单使用

oracle在进行跨库访问时，可以通过创建dblink实现。

创建`database link`,通过`pl/sql developer`图形化创建，通过`Database Links` ---> 右键 ---> new ---> 创建Database Link界面

![](https://luopengfei3000.github.io/assets/images/2019/java/2019-05-01-java-dev-study-question/01.png)

如上图：Owner:当前数据库登录名，Name:随意取个名称，Username:连接数据库的登录名，Password:连接数据库的密码，Database:连接数据库的信息
```
Database:
方式一：
(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=11.218.99.32)(PORT=1521)))(CONNECT_DATA=(SERVICE_NAME =ZAE)))
方式二：
11.218.99.32:1521/ZAE
```
`pl/sql developer` 中使用如下：`select * from test_table@iqs_link t1`

`mapper` 中使用如下：`<![CDATA[ select * from test_table@iqs_link t1 ]]>`

#### 6.mybatis>mapper中直接返回boolean型数据

通常情况下，我们一般会在service中进行真与假的逻辑判断，比如先取出一个对象，根据对象是否为空来判断我们的真假逻辑，这部分可以移动到mapper中进行处理。

示例，我们做一个昵称是否存在的验证：

``` java
<select id="ifExist" parameterType="string" resultType="boolean">
    <![CDATA[ select count(id) from user where name = #{name} ]]>
</select> 
```

mybatis会根据记录数返回值来进行数据的转换

1：true 

0：fasle

注意：如果count>1,那么返回的将还是false，所以我们要确保在数据库中，我们要确保数据唯一性。

#### 7.悲观锁和乐观锁的区别，怎么实现

悲观锁：一段执行逻辑加上悲观锁,不同线程同时执行时,只能有一个线程执行,其他的线程在入口处等待,直到锁被释放。

乐观锁：一段执行逻辑加上乐观锁,不同线程同时执行时,可以同时进入执行,在最后更新数据的时候要检查这些数据是否被其他线程修改了(版本和执行初是否相同),没有修改则进行更新,否则放弃本次操作。
　
乐观锁的实现：
```
update table set x=x+1, version=version+1 where id=#{id} and version=#{version};
```

#### 8.Oracle自定义聚合函数实现字符串连接的聚合

```
select 
       LISTAGG(t.mds_vendor_id, ',') within group(order by t.creation_date) AS mds_vendor_id
from wms_user_vendor_config t
```
将wms_user_vendor_config表中mds_vendor_id字段拼接成逗号分隔的字符串，如果数据量太大，则报错。
因此使用自定义聚合函数实现字符串连接的聚合

```
CREATE OR REPLACE FUNCTION Jion_String_Comma_f(Input VARCHAR2)
  RETURN VARCHAR2
  PARALLEL_ENABLE
  AGGREGATE 
  USING Jion_String_Comma_f_Tp; --最重要是Jion_String_Comma_f_Tp
```

```
CREATE OR REPLACE TYPE "JION_STRING_COMMA_F_TP"                                          AS OBJECT
(
--聚合函数的实质就是一个对象 
--vStr VARCHAR2(4000),
  Vstr Varchar_Array_Tp, --此处也是创建了一个TYPE

  STATIC FUNCTION Odciaggregateinitialize(Sctx IN OUT Jion_String_Comma_f_Tp)
    RETURN NUMBER,
  --对象初始化 
  MEMBER FUNCTION Odciaggregateiterate(SELF  IN OUT Jion_String_Comma_f_Tp,
                                       VALUE IN VARCHAR2) RETURN NUMBER,
  --聚合函数的迭代方法(这是最重要的方法) 
  MEMBER FUNCTION Odciaggregatemerge(SELF IN OUT Jion_String_Comma_f_Tp,
                                     Ctx2 IN Jion_String_Comma_f_Tp)
    RETURN NUMBER,
  --当查询语句并行运行时,才会使用该方法,可将多个并行运行的查询结果聚合 
  MEMBER FUNCTION Odciaggregateterminate(SELF        IN Jion_String_Comma_f_Tp,
                                         Returnvalue OUT VARCHAR2,
                                         Flags       IN NUMBER)
    RETURN NUMBER
 --终止聚集函数的处理,返回聚集函数处理的结果. 
)
```
```
CREATE OR REPLACE TYPE "VARCHAR_ARRAY_TP" IS TABLE OF VARCHAR2(4000)
```

创建TYPE BODY
```
CREATE OR REPLACE TYPE BODY "JION_STRING_COMMA_F_TP" IS
  STATIC FUNCTION Odciaggregateinitialize(Sctx IN OUT Jion_String_Comma_f_Tp)
    RETURN NUMBER IS
  BEGIN
    Sctx := Jion_String_Comma_f_Tp(Varchar_Array_Tp());
    RETURN Odciconst.Success;
  END;

  MEMBER FUNCTION Odciaggregateiterate(SELF  IN OUT Jion_String_Comma_f_Tp,
                                       VALUE IN VARCHAR2) RETURN NUMBER IS
  BEGIN
    Vstr.EXTEND;
    Vstr(Vstr.COUNT) := VALUE;
    RETURN Odciconst.Success;
  END;

  MEMBER FUNCTION Odciaggregatemerge(SELF IN OUT Jion_String_Comma_f_Tp,
                                     Ctx2 IN Jion_String_Comma_f_Tp)
    RETURN NUMBER IS
  BEGIN
    RETURN Odciconst.Success;
  END;

  MEMBER FUNCTION Odciaggregateterminate(SELF        IN Jion_String_Comma_f_Tp,
                                         Returnvalue OUT VARCHAR2,
                                         Flags       IN NUMBER) RETURN NUMBER IS
    Tmp_Vstr VARCHAR2(4000);
  BEGIN
    --添加一条空记录,用作循环最后处理
    FOR Rec_Value IN (SELECT Column_Value FROM TABLE(Vstr)) LOOP

      Tmp_Vstr := substr(Tmp_Vstr || ',' || Rec_Value.Column_Value,1,3999);
      --Tmp_Vstr := Tmp_Vstr || ',' || Rec_Value.Column_Value;

      --太长时截断
      IF Lengthb(Tmp_Vstr) >= 3999 THEN
        EXIT;
      END IF;

    END LOOP;

    Returnvalue := Ltrim(Tmp_Vstr, ',');
    RETURN Odciconst.Success;
  END;
END;
```
此时解决数据量大拼接字段串问题
```
select 
   Jion_String_Comma_f(t.mds_vendor_id) AS mds_vendor_id
from wms_user_vendor_config t
```

