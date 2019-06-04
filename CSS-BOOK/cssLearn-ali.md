# 渡一前端学习（阿里系）

## 一般  
- 行级元素只能嵌套行级元素
- 块级元素可以嵌套任何元素(p标签里面不能套块级元素)
- a标签不能嵌套a标签
- 通用字体 乔布斯
```
font-family:arial
```
- 主流浏览器及内核
```
 ie -trident chorome-webkit safari-webkit firefox -Gecko opera-presto
```
- css优先级和权重

| name | lev |
| ----- | ----- |
| !important | Infinity(无穷) |
| 行间样式 | 10000 |
| id | 100 |
|class、伪类、属性|10|
|标签、伪元素|1|
|通配符|0|

- 行级元素、内联元素 inline
```
特点:内容决定元素所占位置,不可通过css改变宽高
凡是带有inline的元素，都有文字特性
<span> <strong> <em> <a> <del>
```
- display:inline-block
```
是基于下基准线对齐，调整不要vertical-align处理
```
- 初始化样式（针对不同标签样式）
```
text-decoration:none;
font-style:normal;
list-style:none;
padding:0;
margin:0；
```
- body默认margin值：8px
- 盒模型上下左右居中
```
外层盒子的宽高=内层盒子的宽高,再给外层盒子设置padding值
```
- 盒模型的计算
```
width height border padding
```

## 定位
- absolute 绝对定位
```
脱离原来位置进行定位、脱离文档流
相对最近有定位的父级进行定位，如果没有，那么相对文档进行定位
配合 top left;right bottom
```
- relative 相对定位
```
保留原来位置进行定位
配合 top left； right bottom
```
- fixed 固定定位
```
 固定不动
 配合 top left ;right bottom
```
> 注意
>> position:absolute float:left/right 在内部会把元素转换成inline-block

> margin 塌陷
>> 垂直方向margin,父子元素结合到一起了，会去最大那个值
>>> 弥补方式 bfc - block format content(块级格式化上下文)
>>>> ``` position:absolute  display:inline-block float:left/right  overflow:hidden ```

`兄弟机构的margin-bottom和margin-top合并在一起了，可以使外层变成bfc模式，也可通过数学方式解决`

## 浮动
- float:left/right
```
浮动元素产生了浮动流
所有产生了浮动流的元素：
*块级元素看不到他们
*产生了bfc的元素和文本类属性(inline)的元素以及文本都能看到浮动元素
```
- 清除浮动
```
{
  clear:both; // 必须是块级元素
}
// 举个例子
.clear:after, .clear:after {
  content: '\20';
  display: block;
  height: 0;
  clear: both;
}
.clear, .clear {
  *zoom: 1;
}
```

## 伪元素
```
伪元素天生是行级元素，必须加content才能生效
example::after{
  content:'',
  display:'block'
}
```