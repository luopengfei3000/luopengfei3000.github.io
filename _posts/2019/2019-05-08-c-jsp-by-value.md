---
layout: post
title: c标签在两个页面之间的传值及比较
no-post-nav: true
category: java
tags: [front-end]
keywords: java
excerpt : c标签在两个页面之间的传值及比较
---

页面直接的相互传值：

//上传附件页面
```
function uploadManage(id,status){
    var flag = status == "0" ? true : false;
    this.nData = new CommonDialog("upload","790", "500", "avicit/mms/ppm/common/ppmuploadcommon/PpmUploadCommon.jsp?id="+id+"&flag="+flag,"上传",false,true,false);
    this.nData.show();
};
```


点解链接跳转到PpmUploadCommon.jsp页面，也将id和flag传到PpmUploadCommon.jsp页面，此时接收数据如下：

```
<%
	String id = request.getParameter("id");
	boolean flag = Boolean.parseBoolean(request.getParameter("flag"));
%>

<script type="text/javascript">
   var mdsRouteId = "<%=id%>";
   var flag = "<%=flag%>";
</script>
```

使用c标签比较页面上的传值:

如果flag传值为true则显示HelloWorld，反之。（比较的是boolean类型）

```
<c:if test='<%=flag%>'>
	HelloWorld
</c:if>
```

如果id传值为1则显示HelloWorld，反之。（比较的是String类型）

```
<s:if test='<%= id.equals("1") %>’ >
	HelloWorld
</s:if>
```