---
layout: post
title: markdown 使用总结
category: other
tags: [other]
excerpt : markdown来写文章，有一些命令或者是快捷键不是特别熟悉在此记录下来，方便日后查看
---

markdown来写文章，有一些命令或者是快捷键不是特别熟悉在此记录下来，方便日后查看

## 常用语法
### To-do List

```  xml
- [x] 已完成项目1
  - [x] 已完成事项1
  - [x] 已完成事项1
- [ ] 代办事项1
- [ ] 代办事项2
``` 

<label style="color:blue">**效果如下：**</label>

- [x] 已完成项目1
  - [x] 已完成事项1
  - [x] 已完成事项1
- [ ] 代办事项1
- [ ] 代办事项2

### 标题
```  xml
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
``` 

<label style="color:blue">**效果如下：**</label>

# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题

<label style="color:red">**注意：⚠# 和「一级标题」之间建议保留一个字符的空格，这是最标准的 Markdown 写法。**</label>

### 列表

列表格式也很常用，在 Markdown 中，你只需要在文字前面加上 - 就可以了，例如：
```  xml
- 文本1
- 文本2
- 文本3
``` 

<label style="color:blue">**效果如下：**</label>

- 文本1
- 文本2
- 文本3

如果你希望有序列表，也可以在文字前面加上 1. 2. 3. 就可以了，例如：
```  xml
1. 列表1
    1. 列表1.1
    2. 列表1.2
1. 列表2
2. 列表3
``` 

<label style="color:blue">**效果如下：**</label>

1. 列表1
    1. 列表1.1
    2. 列表1.2
1. 列表2
2. 列表3

<label style="color:red">**注意：⚠- 和 1. 和文本之间要保留一个字符的空格。**</label>

### 引用

在我们写作的时候经常需要引用他人的文字，这个时候引用这个格式就很有必要了，在 Markdown 中，你只需要在你希望引用的文字前面加上```   > ``` 就好了

```
> 记录成为更好的自己
```

<label style="color:blue">**效果如下：**</label>

> 记录成为更好的自己

注：> 和文本之间要保留一个字符的空格。

### 链接和图片
在 Markdown 中，插入链接不需要其他按钮，你只需要使用 ```  xml[显示文本](链接地址) ``` 这样的语法即可，例如：
```  xml
[springboot基础学习](https://github.com/luopengfei3000/taotaomultimodule)
``` 
[springboot基础学习](https://github.com/luopengfei3000/taotaomultimodule)

在 Markdown 中，插入图片不需要其他按钮，你只需要使用 ``` ![](图片链接地址)  ```这样的语法即可，例如：
```  xml
![图片浏览](https://luopengfei3000.github.io/assets/images/2019/other/萌宠.jpg)
```

![图片浏览](https://luopengfei3000.github.io/assets/images/2019/other/萌宠.jpg)

<label style="color:red">**注意：⚠插入图片的语法和链接的语法很像，只是前面多了一个 ！**</label>

###  粗体和斜体
Markdown 的粗体和斜体也非常简单，用两个```  *```  包含一段文本就是粗体的语法，用一个```  * ``` 包含一段文本就是斜体的语法。例如：
```  xml
 *一盏灯*， 一片昏黄；**一简书**， 一杯淡茶。 守着那一份淡定， 品读属于自己的寂寞。 保持淡定， 才能欣赏到最美丽
```

<label style="color:blue">**效果如下：**</label>

*一盏灯*， 一片昏黄；**一简书**， 一杯淡茶。 守着那一份淡定， 品读属于自己的寂寞。 保持淡定， 才能欣赏到最美丽

最终显示的就是下文，其中「一盏灯」是斜体，「一简书」是粗体：

### 表格
相关代码：

```  xml
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |
```

<label style="color:blue">**效果如下：**</label>

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |


### 代码高亮

```  xml
 使用``` xxx 开头 ，```结尾的代码块； xxx 代表了语言，比如：Java、xml、js等；
 示例：
  *``` java
     this is java code space
  *```
```
<label style="color:blue">**效果如下：**</label>
``` java
    this is java code space
``` 

### 分割线

```
这是第一段内容
***
---
这是第二段内容
```