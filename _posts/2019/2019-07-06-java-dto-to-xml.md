---
layout: post
title: 组装xml字符串工具类
no-post-nav: true
category: java
tags: [java]
keywords: java
excerpt : 组装xml字符串工具类
---

## 组装xml字符串工具类

需求：将Person类组装成xml格式

例如：
```
Person p = new Person();
p.setId("123");
p.setName("张三");
p.setAge(30);

转成如下格式

<persons>
    <person>
        <id>123</id>
        <name>张三</name>
        <age>30</age>
    </person>
</persons>
```

**实现如下**

Elenment类:
``` java
public class Element {
    private String name; // 元素名
    private String nodeText = ""; // 文本值
    private Map<String, String> property = new HashMap<String, String>(); // 属性
    private boolean isleaf = true; // 是否子节点
    private List<Element> child = new ArrayList<Element>(); // 子节点

    public Element(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getNodeText() {
        return nodeText;
    }

    public void setNodeText(String nodeText) {
        this.nodeText = nodeText;
    }

    public Map<String, String> getProperty() {
        return property;
    }

    public void setProperty(Map<String, String> property) {
        this.property = property;
    }

    public boolean isIsleaf() {
        return isleaf;
    }

    // 这个方法应该屏蔽
    public void setIsleaf(boolean isleaf) {
        this.isleaf = isleaf;
    }

    public List<Element> getChild() {
        return child;
    }

    public void setChild(List<Element> child) {
        this.child = child;
        if (this.isleaf && this.child.size() > 0) {
            this.isleaf = false;
        }
    }

    /**
     * 添加属性
     * @param key 
     * @param value
     * @return void
     */
    public void addProperty(String key, String value) {
        this.property.put(key, value);
    }

    /**
     * 添加子节点
     * @param el 
     * @return void
     */
    public void addChild(Element el) {
        this.child.add(el);
        if (this.isleaf && this.child.size() > 0) {
            this.isleaf = false;
        }
    }
}
```

XmlUtils类:
``` java
public class XmlUtils {
    public static String lt = "<";
    public static String ltEnd = "</";
    public static String rt = ">";
    public static String rhtEnd = "/>";
    public static String quotes = "\"";
    public static String equal = "=";
    public static String blank = " ";

    /**
     * 拼接xml个元素信息
     * @param element 
     * @return java.lang.StringBuffer
     * @Author luopf 2019/7/5 13:39
     */
    public static StringBuffer elementToXml(Element element) {
        StringBuffer result = new StringBuffer();
        // 元素开始
        result.append(lt).append(element.getName());
        // 判断是否有属性
        if (element.getProperty() != null && element.getProperty().size() > 0) {
            Iterator<String> iterator = element.getProperty().keySet().iterator();
            while (iterator.hasNext()) {
                String key = String.valueOf(iterator.next());
                String value = element.getProperty().get(key);
                result.append(blank).append(key).append(equal).append(quotes).append(value).append(quotes).append(blank);
            }
        }
        // 结束的标记
        result.append(rt);
        /**
         * 判断是否是叶子节点，如果是叶子节点，需要添加节点内容，不是叶子节点，那么循环添加子节点
         */
        if (element.isIsleaf()) {
            result.append(element.getNodeText());
        } else {
            for (Element temp : element.getChild()) {
                result.append(elementToXml(temp));
            }
        }
        // 元素结束
        result.append(ltEnd).append(element.getName()).append(rt);
        return result;
    }

    /**
     * 拼接xml申明信息
     * @param element 
     * @return java.lang.String
     * @Author luopf 2019/7/5 13:39
     */
    public static String element2XML(Element element) {
        StringBuffer body = elementToXml(element);
        StringBuffer head = new StringBuffer("<?xml version=\"1.0\" encoding=\"UTF-8\"?>");
        head.append(body);
        return head.toString();
    }
}
```

Test类：

``` java
public class Test {
    public static void main(String[] args){
    	//单个对象拼接
	    /*PmmEquipMaintenanceDTO dto = new PmmEquipMaintenanceDTO();
	    dto.setId("123");
	    dto.setEquipNo("code");
	    dto.setUpkeepStartDate(new Date());
	    dto.setUpkeepEndDate(new Date());
	    dto.setMaintenanceType("1");

	    Element pmmEquipMaintenances = new Element("pmmEquipMaintenances");
	    Element pmmEquipMaintenance = new Element("pmmEquipMaintenance");
	    Element id = new Element("id");
		id.setNodeText(dto.getId());
	    Element code = new Element("code");
	    code.setNodeText(dto.getEquipNo());
	    Element startTime = new Element("startTime");
	    startTime.setNodeText(DateUtil.getDateStr(dto.getUpkeepStartDate()));
	    Element endTime = new Element("endTime");
	    endTime.setNodeText(DateUtil.getDateStr(dto.getUpkeepEndDate()));
	    Element type = new Element("type");
	    type.setNodeText(dto.getMaintenanceType());
	    pmmEquipMaintenance.addChild(id);
	    pmmEquipMaintenance.addChild(code);
	    pmmEquipMaintenance.addChild(startTime);
	    pmmEquipMaintenance.addChild(endTime);
	    pmmEquipMaintenance.addChild(type);
	    pmmEquipMaintenances.addChild(pmmEquipMaintenance);
	    String result = XmlUtils.element2XML(pmmEquipMaintenances);
	    System.out.println(result);*/

	    //id拼接
	    /*String[] ids = {"1","2"};
	    Element pmmEquipMaintenances = new Element("ids");
	    for (String id : ids) {
		    Element elementid = new Element("id");
		    elementid.setNodeText(id);
		    pmmEquipMaintenances.addChild(elementid);
	    }
	    String result = XmlUtils.element2XML(pmmEquipMaintenances);
		System.out.println(result);*/

	    //多个对象拼接
	    List<PmmEquipMaintenanceDTO> list = new ArrayList<PmmEquipMaintenanceDTO>();
	    PmmEquipMaintenanceDTO dto = new PmmEquipMaintenanceDTO();
	    dto.setId("123");
	    dto.setEquipNo("code");
	    dto.setUpkeepStartDate(new Date());
	    dto.setUpkeepEndDate(new Date());
	    dto.setMaintenanceType("1");

        PmmEquipMaintenanceDTO dto1 = new PmmEquipMaintenanceDTO();
        dto1.setId("123");
        dto1.setEquipNo("code");
        dto1.setUpkeepStartDate(new Date());
        dto1.setUpkeepEndDate(new Date());
        dto1.setMaintenanceType("1");

        list.add(dto);
        list.add(dto1);

        Element pmmEquipMaintenances = new Element("pmmEquipMaintenances");
        for (PmmEquipMaintenanceDTO pmmEquipMaintenanceDTO : list) {
            Element pmmEquipMaintenance = new Element("pmmEquipMaintenance");
            Element id = new Element("id");
            id.setNodeText(pmmEquipMaintenanceDTO.getId());
            Element code = new Element("code");
            code.setNodeText(pmmEquipMaintenanceDTO.getEquipNo());
            Element startTime = new Element("startTime");
            startTime.setNodeText(DateUtil.getDateStr(pmmEquipMaintenanceDTO.getUpkeepStartDate()));
            Element endTime = new Element("endTime");
            endTime.setNodeText(DateUtil.getDateStr(pmmEquipMaintenanceDTO.getUpkeepEndDate()));
            Element type = new Element("type");
            type.setNodeText(pmmEquipMaintenanceDTO.getMaintenanceType());
            pmmEquipMaintenance.addChild(id);
            pmmEquipMaintenance.addChild(code);
            pmmEquipMaintenance.addChild(startTime);
            pmmEquipMaintenance.addChild(endTime);
            pmmEquipMaintenance.addChild(type);
            pmmEquipMaintenances.addChild(pmmEquipMaintenance);
        }

        String result = XmlUtils.element2XML(pmmEquipMaintenances);
        System.out.println(result);
    }
    
    //特殊格式拼接
    Element el = new Element("node");
    Element el1 = new Element("node1");
    el1.addProperty("name", "zhangshan");
    el1.addProperty("password", "123465");
    el1.setNodeText("11111");
    el.addChild(el1);
    
    Element el2 = new Element("node1");
    el2.addProperty("name", "lisi");
    el2.addProperty("password", "3333");
    el2.setNodeText("11111");
    el.addChild(el2);
    System.out.println(XmlUtils.elementToXml(el));
}
```

结果：
```
//单个对象拼接
<?xml version="1.0" encoding="UTF-8"?>
<pmmEquipMaintenances>
    <pmmEquipMaintenance>
        <id>123</id>
        <code>code</code>
        <startTime>2019-07-05</startTime>
        <endTime>2019-07-05</endTime>
        <type>1</type>
    </pmmEquipMaintenance>
</pmmEquipMaintenances>

//id拼接
<?xml version="1.0" encoding="UTF-8"?>
<ids>
	<id>1</id>
	<id>2</id>
</ids>

//多个对象拼接
<?xml version="1.0" encoding="UTF-8"?>
<pmmEquipMaintenances>
	<pmmEquipMaintenance>
		<id>123</id>
		<code>code</code>
		<startTime>2019-07-05</startTime>
		<endTime>2019-07-05</endTime>
		<type>1</type>
	</pmmEquipMaintenance>
	<pmmEquipMaintenance>
		<id>123</id>
		<code>code</code>
		<startTime>2019-07-05</startTime>
		<endTime>2019-07-05</endTime>
		<type>1</type>
	</pmmEquipMaintenance>
</pmmEquipMaintenances>

//特殊格式拼接
<node>
    <node1 name="zhangshan"  password="123465" >11111</node1>
    <node1 name="lishi"  password="3333" >11111</node1>
</node>
```
