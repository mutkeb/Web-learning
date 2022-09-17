# 第一章——HTML进阶

## 常见的语义化标签

头部——header

主体——main

底部——footer

导航——nav

侧边栏——aside

区域快——section

# 第二章——CSS伪类

## CSS伪元素——::after/::before

```css
/* before */
选择器::before{
  /* 使用空白符号占位 */
  content: '';
}

/* after */
选择器::after{
  /* 使用空白符号占位 */
  content: '';
}
```

伪元素就是利用 `css`代码在标签内部的前面，或者后面添加一个行内元素，这个行内元素可以理解为 `span`

## CSS伪类--清除浮动

让父元素包住浮动的子元素，这种技术被称为清除浮动

### 使用场景

当两个父元素相邻，但第一个父元素又是子元素的高度撑起来的，所以为了让第二个父元素不要与第一个父元素下边界紧跟。

![image-20220902102832924](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902102832924.png)

![image-20220902102846234](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902102846234.png)

### 解决方法

```css
.clearfix::after{
  content: '';
  display: block;
  clear: both;
}
```

```html
<!-- 添加清除浮动类名 -->
<div class="father-one clearfix">
    <div class="son-one">son-one</div>
    <div class="son-two">son-two</div>
    <div class="son-three">son-three</div>
</div>
```

## CSS伪类--事件伪类

### `hover`——鼠标移动上去

```css
li:hover{
    background-color: #47A0FC;
    color: white;
}
```

### `active` ——鼠标点击时

```css
ul>li:active{
    /* 要改变的效果 */
    color: black;
}
```

`hover`一定要在 `active`之前，否则不会生效

```css
/* 正确 */
a:hover{}
a:active{}
/* 错误 */
a:active{}
a:hover{}
```

### `focus`——获取焦点后

## CSS伪类--列表伪类

### 匹配第一个子元素--`:first-child`

![image-20220902103441683](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902103441683.png)

```css
ul>li:first-child{
    background-color: #3687FC;
    color: #FFFFFF;
}
```

### 匹配最后一个子元素-- `:last-child`

### 匹配第n个子元素-- `:nth-child()`

```css
ul>li:nth-child(3){
  background-color: #3687FC;
  color: #FFFFFF;
}
```

![image-20220902103603387](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902103603387.png)

### 消除元素的一些操作

1. width设置为0
2. display设置为none

# 第三章——CSS装饰

## CSS装饰--cursor

```css
p{
    cursor: pointer;
}
```

改变鼠标箭头的形状

![image-20220902104326221](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902104326221.png)

## CSS装饰--box-shadow/text-shadow

### box-shadow——盒子阴影

```css
div{
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.11);
}
```

```
/* x 偏移量 | y 偏移量 | 阴影模糊半径 | 阴影颜色 */
box-shadow: 10px 5px 5px black;
```

### text-shadow——文字阴影

```css
span{
    text-shadow: 0px 0px 10px rgba(0, 0, 0, 0.11);
}
```

# 第四章——CSS Flex布局

## CSS Flex布局

### 什么是flex

Flex：意为弹性布局

它最显著的效果就是把原本从上到下排列的块状元素变成水平排列

![image-20220902105332255](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902105332255.png)

```html
<div class="container">
  <div class="item">项目1</div>
  <div class="item">项目2</div>
  <div class="item">项目3</div>
</div>
```

```css
.container {
  display: flex;
  background: #D5E8D4;
  border: 1px solid #5D9E5A;
}

.item {
  width: 50px;
  height: 50px;
  background: #FFF2CC;
  border: 1px solid #B7A570;
  margin: 10px;
}
```

### 注意

设为Flex布局之后，子元素的float、clear和vertical-align属性将失效

## justify-content和align-items

### justify-content

定义水平方向上的对齐方式

![img](https://document.youkeda.com/P3-2-HTML-CSS/1.4/9.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

### align-items

![img](https://document.youkeda.com/P3-2-HTML-CSS/1.4/10.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

## flex-wrap

### 样式

![img](https://document.youkeda.com/P3-2-HTML-CSS/1.4/19.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

```css
.container {
  width: 300px;
  display: flex;
  flex-wrap: wrap;
}

.item {
  width: 50px;
}
```

### 作用

防止项目宽度被压缩

## flex：none和flex：1

### flex：none

不允许项目压缩、放大默认情况，不设置flex属性

1. ![image-20220902110508866](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902110508866.png)

2. 设置了flex:none

   ![image-20220902110533084](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902110533084.png)

### flex：1

项目自动充满剩余空间 

1. 默认情况

   ![image-20220902110606471](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902110606471.png)

2. 设置了flex：1

   ![image-20220902110619157](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902110619157.png)

### 两栏自适应布局

一栏固定宽度，另一栏随浏览器宽度的变化自动调节自己的宽度

```html
<div class="flex-auto">
  <div class="static"></div>
  <div class="flexible"></div>
</div>
```

```css
.flex-auto {
  display: flex;
}
.flex-auto .static {
  width: 100px;
  flex: none;
}
.flex-auto .flexible {
  flex: 1;
}
```

### 指定项目放大

一行有剩余的情况下，我们可以任意指定某个项目放大撑满剩余空间

1. 默认不设置flex属性

   ![image-20220902110915100](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902110915100.png)

2. 最后一个项目设置了flex：1

   ![image-20220902110928413](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220902110928413.png)

## flex-direction

![img](https://document.youkeda.com/P3-2-HTML-CSS/1.4/8.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

# 第五章——CSS高级美化

## 单行文本超出省略

1. 强制不换行：white-space：nowrap

2. 元素内容溢出：overflow

   > 决定超出盒子的内容怎么显示

3. 文本溢出省略 text-overflow

> 处理文本溢出的属性

```css
/* 强制不换行 */
white-space: nowrap;
/* 隐藏超出部分 */
overflow: hidden;
/* 用省略号代替剩余内容 */
text-overflow: ellipsis;
```

## 多行文本超出省略

```css
/* 隐藏超出部分 */
overflow : hidden;
/* 文本超出就用省略号 */
text-overflow: ellipsis;
/* 把对象作为弹性伸缩盒子模型显示 */
display: -webkit-box;
/* WebKit内核的浏览器的私有属性，设置文本超出2行就用省略号 */
-webkit-line-clamp: 2;
/* WebKit内核的浏览器的私有属性，设置或检索伸缩盒对象的子元素的排列方式 */
-webkit-box-orient: vertical;
```

# 第六章——CSS预处理器

## 变量

### 使用变量

```scss
// 变量需要定义在使用之前
$position: "absolute";
$mainTxtColor: '#333';

// 应用
#main {
  position: $position;
  color: $mainTxtColor;
}
```

### 简单计算

```scss
$width: 10px;

#main {
  width: $width / 2;
}
```

### 插值法

#{}插值几乎可以在Sass样式表的任何地方使用

```scss
$name: "mail";
$top-or-bottom: "top";
$left-or-right: "left";

.icon-#{$name} {
  background-image: url("/icons/#{$name}.svg");
  position: absolute;
  #{$top-or-bottom}: 0;
  #{$left-or-right}: 0;
}
```

## 复用：minin/include

解决代码复用问题，还能解决无语义的类名问题

### 无参数混合

```scss
@mixin square {
  width: 100px;
  height: 100px;
}

// 应用：
.user-avatar {
  @include square;
}
.admin-avatar {
  @include square;
}
```

#### 编译结果

```css
.user-avatar {
  width: 100px;
  height: 100px;
}

.admin-avatar {
  width: 100px;
  height: 100px;
}
```

### 有参数混合

```scss
@mixin square($size) {
  width: $size;
  height: $size;
}

// 应用
.avatar {
  @include square(100px);
}
```

#### 编译结果

```css
.avatar {
  width: 100px;
  height: 100px;
}
```

### 传参有默认值

```scss
@mixin square($size: 100px) {
  width: $size;
  height: $size;
}

// 不传参数就会使用默认的值 100px
.avatar {
  @include square;
}

// 传入参数就会使用传入的值 200px
.avatar-200 {
  @include square($size: 200px);
}
```

#### 编译结果

```css
/* 不传参数就会使用默认的值 100px */
.avatar {
  width: 100px;
  height: 100px;
}
/* 传入参数就会使用传入的值 200px */
.avatar-200 {
  width: 200px;
  height: 200px;
}
```

# 第七章——响应式布局

## 媒体查询

### 作用

根据浏览器宽度的不同可以有不同的布局

### 条件：最大宽度

![img](https://document.youkeda.com/P3-2-HTML-CSS/1.7/18.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

### 条件：最小宽度

![img](https://document.youkeda.com/P3-2-HTML-CSS/1.7/20.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

### 逻辑操作符 and

![img](https://document.youkeda.com/P3-2-HTML-CSS/1.7/21.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

## 断点

![img](https://document.youkeda.com/P3-2-HTML-CSS/1.7/17.jpg?x-oss-process=image/resize,w_800/watermark,image_d2F0ZXJtYXNrLnBuZz94LW9zcy1wcm9jZXNzPWltYWdlL3Jlc2l6ZSx3XzEwMA==,t_60,g_se,x_10,y_10)

### 原因

css中越靠后的样式优先级越高，所以在前面的是范围最大的。