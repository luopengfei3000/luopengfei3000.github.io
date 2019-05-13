---
layout: post
title: Mybatis关联查询之一对多和多对一XML配置详解
no-post-nav: true
category: mybatis
tags: [front-end]
keywords: mybatis
excerpt : Mybatis关联查询之一对多和多对一XML配置详解
---

## Mybatis关联查询之一对多和多对一XML配置详解

### 查询配置信息表(一对多)

#### 首先是有两张表(配置信息表mds_config和配置信息子表mds_config_l表)。表结构如下图

![](https://luopengfei3000.github.io/assets/images/2019/mybatis/2019-03-28-mybatis-one-to-many/01.png)

![](https://luopengfei3000.github.io/assets/images/2019/mybatis/2019-03-28-mybatis-one-to-many/02.png)

#### 创建实体bean

MdsConfigDTO.java
```
public class MdsConfigDTO{
    private static final long serialVersionUID = 1L;
    /*
     *主键
     */
    private java.lang.String id;
    /*
     *配置编码
     */
    private java.lang.String configCode;
    /*
     *配置名称
     */
    private java.lang.String configName;
    /*
     *是否有效 ^ 0-无效，1-有效
     */
    private java.lang.String validFlag;
    /*
     *备注
     */
    private java.lang.String note;

	@Transient
	private List<MdsConfigLDTO> mdsConfigLDTOs;

```
MdsConfigLDTO.java：

```
public class MdsConfigLDTO {
    private static final long serialVersionUID = 1L;
    /*
     *主键
     */
    private java.lang.String id;
    /*
     *配置信息主表ID
     */
    private java.lang.String mdsConfigId;
    /*
     *显示顺序
     */
    private Long innerOrder;
    /*
     *明细名称
     */
    private java.lang.String detailName;
    /*
     *明细值
     */
    private java.lang.String detailValue;
    /*
     *是否有效 ^ 0-无效，1-有效
     */
    private java.lang.String validFlag;
```
#### 配置Mapper.xml文件
##### 第一种配置Mapper.xml文件：

```
<resultMap id="MdsConfigDTOMap" type="mms.mds.mdsconfig.dto.MdsConfigDTO">
		<result property="id" column="ID" jdbcType="VARCHAR" />
		<result property="configCode" column="CONFIG_CODE" jdbcType="VARCHAR" />
		<result property="configName" column="CONFIG_NAME" jdbcType="VARCHAR" />
		<result property="validFlag" column="VALID_FLAG" jdbcType="VARCHAR" />
		<result property="note" column="NOTE" jdbcType="VARCHAR" />

		<collection property="mdsConfigLDTOs" ofType="mms.mds.mdsconfig.dto.MdsConfigLDTO" column="id"><!--这里把mds_config的id传过去 -->
			<result column="mds_config_l_id" property="id"/><!-- 这里的column对应的是下面查询的别名，而不是表字段名 -->
			<result column="detail_name" property="detailName"/><!-- property对应JavaBean中的属性名 -->
			<result column="detail_value" property="detailValue"/>
		</collection>
</resultMap>

<select id="searchMdsConfigByPage" parameterType="java.util.Map" resultMap="MdsConfigDTOMap">
	select
		t1.id                             ,
		t1.config_code                    ,
		t1.config_name                    ,
		t1.valid_flag                     ,
		t1.note                           ,
		t2.id as mds_config_l_id          ,
		t2.detail_name                    ,
		t2.detail_value
	from mds_config t1
		left join mds_config_l t2
	on t1.id = t2.mds_config_id
</select>
```

##### 第二种配置Mapper.xml文件：

1.配置配置信息表MdsConfigMappper.xml文件
```
<resultMap id="MdsConfigDTOMap" type="mms.mds.mdsconfig.dto.MdsConfigDTO">
		<result property="id" column="ID" jdbcType="VARCHAR" />
		<result property="configCode" column="CONFIG_CODE" jdbcType="VARCHAR" />
		<result property="configName" column="CONFIG_NAME" jdbcType="VARCHAR" />
		<result property="validFlag" column="VALID_FLAG" jdbcType="VARCHAR" />
		<result property="note" column="NOTE" jdbcType="VARCHAR" />

		<collection property="mdsConfigLDTOs" ofType="mms.mds.mdsconfig.dto.MdsConfigLDTO" select="mms.mds.mdsconfig.dao.MdsConfigLDao.findMdsConfigLByPid" column="id">
		</collection>
</resultMap>
<!-- 分页查询 MDS_CONFIG -->
<select id="searchMdsConfigByPage" parameterType="java.util.Map" resultMap="MdsConfigDTOMap">
	select
		t1.id                             ,
		t1.config_code                    ,
		t1.config_name                    ,
		t1.valid_flag                     ,
		t1.note                           
	from mds_config t1
</select>
```
2.配置信息子表MdsConfigLMappper.xml文件
```
<mapper namespace="mms.mds.mdsconfig.dao.MdsConfigLDao">

<resultMap id="MdsConfigLDTOMap" type="mms.mds.mdsconfig.dto.MdsConfigLDTO">
		<result property="id" column="ID" jdbcType="VARCHAR" />
		<result property="mdsConfigId" column="MDS_CONFIG_ID" jdbcType="VARCHAR" />
		<result property="innerOrder" column="INNER_ORDER" jdbcType="DECIMAL" />
		<result property="detailName" column="DETAIL_NAME" jdbcType="VARCHAR" />
		<result property="detailValue" column="DETAIL_VALUE" jdbcType="VARCHAR" />
		<result property="validFlag" column="VALID_FLAG" jdbcType="VARCHAR" />
</resultMap>
<!-- 根据pid查询对象 MDS_CONFIG_L -->
<select id="findMdsConfigLByPid" parameterType="java.util.Map" resultMap="MdsConfigLDTOMap">
	select
		t1.id                             ,
		t1.mds_config_id                  ,
		t1.inner_order                    ,
		t1.detail_name                    ,
		t1.detail_value                   ,
		t1.valid_flag                       
	from mds_config_l 	t1
		where t1.mds_config_id = #{pid}
</select>
```
测试结果：

![](https://luopengfei3000.github.io/assets/images/2019/mybatis/2019-03-28-mybatis-one-to-many/03.png)

### 查询配置信息子表(多对一)

1.配置信息子表MdsConfigLMappper.xml文件
```
<resultMap id="MdsConfigLDTOMap" type="mms.mds.mdsconfig.dto.MdsConfigLDTO">
		<result property="id" column="ID" jdbcType="VARCHAR" />
		<result property="mdsConfigId" column="MDS_CONFIG_ID" jdbcType="VARCHAR" />
		<result property="innerOrder" column="INNER_ORDER" jdbcType="DECIMAL" />
		<result property="detailName" column="DETAIL_NAME" jdbcType="VARCHAR" />
		<result property="detailValue" column="DETAIL_VALUE" jdbcType="VARCHAR" />
		<result property="validFlag" column="VALID_FLAG" jdbcType="VARCHAR" />

		<association property="mdsConfigDTO" select="mms.mds.mdsconfig.dao.MdsConfigDao.findMdsConfigById" column="mds_config_id" javaType="mms.mds.mdsconfig.dto.MdsConfigDTO">
			<!-- 这里要注意的是column对应的是mds_config_l中的外键，而且需是表字段名 -->
		</association>
</resultMap>
<!-- 分页查询 MDS_CONFIG_L -->
<select id="searchMdsConfigLByPage" parameterType="java.util.Map" resultMap="MdsConfigLDTOMap">
	select
		t1.id                             ,
		t1.mds_config_id                  ,
		t1.inner_order                    ,
		t1.detail_name                    ,
		t1.detail_value                   ,
		t1.valid_flag                                  
	from mds_config_l t1
</select>
```

2.配置信息表MdsConfigMappper.xml文件
```
<mapper namespace="mms.mds.mdsconfig.dao.MdsConfigDao">

<resultMap id="MdsConfigDTOMap" type="mms.mds.mdsconfig.dto.MdsConfigDTO">
		<result property="id" column="ID" jdbcType="VARCHAR" />
		<result property="configCode" column="CONFIG_CODE" jdbcType="VARCHAR" />
		<result property="configName" column="CONFIG_NAME" jdbcType="VARCHAR" />
		<result property="validFlag" column="VALID_FLAG" jdbcType="VARCHAR" />
		<result property="note" column="NOTE" jdbcType="VARCHAR" />
</resultMap>
<!-- 主键查询对象 MDS_CONFIG -->
<select id="findMdsConfigById" parameterType="java.util.Map" resultMap="MdsConfigDTOMap">
	select
		t1.id                             ,
		t1.config_code                    ,
		t1.config_name                    ,
		t1.valid_flag                     ,
		t1.note                                              
	from mds_config t1
		where t1.id = #{id}
</select>
```

测试结果：

![](https://luopengfei3000.github.io/assets/images/2019/mybatis/2019-03-28-mybatis-one-to-many/04.png)