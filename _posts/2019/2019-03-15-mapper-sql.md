---
layout: post
title: 将一个mapper中的sql片段引用到其他mapper中
no-post-nav: true
category: mybatis
tags: [mybatis]
keywords: mybatis
excerpt : 将一个mapper中的sql片段引用到其他mapper中
---

### 1.表别名必须一致（必须为item）
mdsItemMapper文件内容如下：
```
<mapper namespace="avicit.mms.mds.mdsitem.dao.MdsItemDao">
<!-- 将四个字段拼接起来，作为通用sql -->
<!-- 物料关键属性(表别名item) -->
<sql id="keyattribute_sql">
	item.MARQUE || ',' || item.SPECS || ',' || item.TECH_CONDITION || ',' || item.DELIVERY_STATUS
</sql>
	
<!-- 物料关键属性-查询列 -->
<sql id="select_keyAttribute_sql">
	TRIM(',' FROM
		REPLACE(REPLACE(<include refid="avicit.mms.mds.mdsitem.dao.MdsItemDao.keyattribute_sql"/>,
						',,,',
						','),
				',,',
				','))
</sql>
</mapper>
```
将mdsItemMapper中的sql片段(通用sql片段)引用到PmsRequireReceiveMapper中：
```
<select id="searchPmsRequireReceiveByPage" parameterType="java.util.Map" resultMap="PmsRequireReceiveDTOMap">
	select
        item.item_code mds_item_code        ,
        item.item_name mds_item_name        ,
        <include refid="avicit.mms.mds.mdsitem.dao.MdsItemDao.select_keyAttribute_sql"/> key_attribute
   FROM pms_require_receive t1
       LEFT JOIN mds_item item 
   ON item.id = t1.mds_item_id
	<where>
		<if test="bean != null">
			<if test="bean.keyAttribute != null and bean.keyAttribute != ''">
				and <include refid="avicit.mms.mds.mdsitem.dao.MdsItemDao.keyattribute_sql"/> like '%'||#{bean.keyAttribute}||'%'
			</if>
		</if>
	</where>
	order by t1.inner_order asc
</select>
```

### 2.表别名可以随意（使用 ${} 占位符参数化）

**使用 ${} 占位符参数化的（注意，此处的参数不是调用时传进来的），不同的属性值通过包含的实例而变化**
```
<mapper namespace="avicit.mms.mds.mdsitem.dao.MdsItemDao">
<!-- 将四个字段拼接起来，作为通用sql -->
<!-- 物料关键属性 -->
<sql id="keyattribute_sql">
	${alias}.MARQUE || ',' || ${alias}.SPECS || ',' || ${alias}.TECH_CONDITION || ',' || ${alias}.DELIVERY_STATUS
</sql>
	
<!-- 物料关键属性-查询列 -->
<sql id="select_keyAttribute_sql">
	TRIM(',' FROM
		REPLACE(REPLACE(<include refid="avicit.mms.mds.mdsitem.dao.MdsItemDao.keyattribute_sql"/>,
						',,,',
						','),
				',,',
				','))
</sql>
</mapper>
```
将mdsItemMapper中的sql片段(通用sql片段)引用到PmsRequireReceiveMapper中：
```
<select id="searchPmsRequireReceiveByPage" parameterType="java.util.Map" resultMap="PmsRequireReceiveDTOMap">
	select
        item.item_code mds_item_code        ,
        item.item_name mds_item_name        ,
        <include refid="avicit.mms.mds.mdsitem.dao.MdsItemDao.select_keyAttribute_sql"><property name="alias" value="t2"/></include> key_attribute
   FROM pms_require_receive t1
       LEFT JOIN mds_item t2 
   ON t2.id = t1.mds_item_id
	<where>
		<if test="bean != null">
			<if test="bean.keyAttribute != null and bean.keyAttribute != ''">
				and <include refid="avicit.mms.mds.mdsitem.dao.MdsItemDao.keyattribute_sql"><property name="alias" value="t2"/></include> like '%'||#{bean.keyAttribute}||'%'
			</if>
		</if>
	</where>
	order by t1.inner_order asc
</select>
```






