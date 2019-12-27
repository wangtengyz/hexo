---
title: Less语法笔记
# categories: 2019-12-20 20:10:41
tags: Less
description: Less语法
date: 2019-12-20 20:10:41
---
## Less语法
****
### 一、变量

#### 1.普通变量

less 以@开头定义变量，并且使用时直接键入@名称。注意：作为属性值的变量不能添加大括号（{}）
```
@color:#333;
Div{
  Background：@color；
}
```
Less其变量只能定义一次，不能重复定义，否则后面的会类似与js的变量提升，覆盖前面的变量。

使用变量设置css，也方便代码的维护。
<!-- more -->

#### 2.选择器变量

选择器变量让选择器变成一个动态的。作为选择器的变量在使用的时候需要添加大括号（{}），变量的前面可以添加选择操作符。
```
@mySelector: #wrap;
@Wrap: wrap;
@{mySelector}{ //变量名 必须使用大括号包裹
  color: #999;
  width: 50%;
}
.@{Wrap}{
  color:#ccc;
}
#@{Wrap}{
  color:#666;
}  
/* 生成的 CSS */
#wrap{
  color: #999;
  width: 50%;
}
.wrap{
  color:#ccc;
}
#wrap{
  color:#666;
}
```

#### 3.属性变量
属性变量使用的时候也需要添加大括号（{}）
```
@borderStyle: border-style;
@Soild:solid;
#wrap{
  @{borderStyle}: @Soild;//变量名 必须使用大括号包裹
}
/* 生成的 CSS */
#wrap{
  border-style:solid;
}
```

#### 4.Url变量
项目结构改变时，可以方便我们修改，目的是为了方便项目的维护。
```
@images: "../img";//需要加引号
body {
  background: url("@{images}/dog.png");//变量名 必须使用大括号包裹
}
/* 生成的 CSS */
body {
  background: url("../img/dog.png");
}
```

#### 5.用变量的定义变量
```
@fnord:  "I am fnord.";
@var:    "fnord";
#wrap::after{
  content: @@var; //将@var替换为其值 content:@fnord;
}
/* 生成的 CSS */
#wrap::after{
  content: "I am fnord.";
}
```

### 二、混合方法

#### 1.无参数方法 方法如声明的集合，使用值直接键入名称即可
```
.card(){
//something....
}
#wrap{
  .card();
}
```

#### 2.具体参数方法
```
.setSize(@width_size,@height_size){
  width:@width_size;
  height:@height_size;
}
```

#### 3.默认参数方法
```
.border(@a:10px,@b:50px,@c:30px,@color:#000){
    border:solid 1px @color;
    box-shadow: @arguments;//指代的是 全部参数
}
#main{
    .border(0px,5px,30px,red);//必须带着单位
}
#wrap{
    .border(0px);
}
#content{
    .border;//等价于 .border()
}

/* 生成的 CSS */
#main{
    border:solid 1px red;
    box-shadow:0px,5px,30px,red;
}
#wrap{
    border:solid 1px #000;
    box-shadow: 0px 50px 30px #000;
}
#content{
    border:solid 1px #000;
    box-shadow: 10px 50px 30px #000;
}    
```

#### 4.不定参
不确定参数的个数
```
.boxShadow(...){
    box-shadow: @arguments;
}
.textShadow(@a,...){
    text-shadow: @arguments;
}
#main{
    .boxShadow(1px,4px,30px,red);
    .textShadow(1px,4px,30px,red);
}
/* 生成后的 CSS */
#main{
  box-shadow: 1px 4px 30px red;
  text-shadow: 1px 4px 30px red;
}
```

#### 4.条件语句
Less没有if / else 但是less具有一个when，and，not与“，”语法。
```
#card{
    // and 运算符 ，相当于 与运算 &&，必须条件全部符合才会执行
    .border(@width,@color,@style) when (@width>100px) and(@color=#999){
        border:@style @color @width;
    }
    // not 运算符，相当于 非运算 !，条件为 不符合才会执行
    .background(@color) when not (@color>=#222){
        background:@color;
    }
    // , 逗号分隔符：相当于 或运算 ||，只要有一个符合条件就会执行
    .font(@size:20px) when (@size>50px) , (@size<100px){
        font-size: @size;
    }
}
#main{
    #card>.border(200px,#999,solid);
    #card .background(#111);
    #card > .font(40px);
}
/* 生成后的 CSS */
#main{
  border:solid #999 200px;
  background:#111;
  font-size:40px;
}
```

### 三、属性的拼接语法
+代表的是逗号，+_代表的是空格。

#### 1.空格
```
.Animation() {
    transform+_: scale(2);
}
.main {
    .Animation();
    transform+_: rotate(15deg);
}

/* 生成的 CSS */
.main {
    transform: scale(2) rotate(15deg);
}
```

#### 2.逗号
```
.boxShadow() {
    box-shadow+: inset 0 0 10px #555;
}
.main {
    .boxShadow();
    box-shadow+: 0 0 20px black;
}
/* 生成后的 CSS */
.main {
    box-shadow: inset 0 0 10px #555, 0 0 20px black;
}
```

### 四、继承(扩展)
extend是less的一个伪类。它可以继承所匹配声明中的全部样式。
```
.animation{
    transition: all .3s ease-out;
    .hide{
        transform:scale(0);
    }
}
#main{
    &:extend(.animation);
}
#con{
    &:extend(.animation .hide);
}

/* 生成后的 CSS */
.animation,#main{
    transition: all .3s ease-out;
}
.animation .hide , #con{
    transform:scale(0);
}
```

### 五、less中可以使用js

less本身是使用js实现的，所以在less中可以使用js。Js的代码写在字符串模板里。

```
@content:`"aaa".toUpperCase()`;
#randomColor{
    @randomColor: ~"rgb(`Math.round(Math.random() * 256)`,`Math.round(Math.random() * 256)`,`Math.round(Math.random() * 256)`)";
}
#wrap{
    width: ~"`Math.round(Math.random() * 100)`px";
    &:after{
        content:@content;
    }
    height: ~"`window.innerHeight`px";
    alert:~"`alert(1)`";
    #randomColor();
    background-color: @randomColor;
}
/* 生成后的 CSS */

// 弹出 1
#wrap{
    width: 随机值（0~100）px;
    height: 743px;//由电脑而异
    background: 随机颜色;
}
#wrap::after{
    content:"AAA";
}
```

### 六、Less的函数使用

参考手册：http://lesscss.cn/functions/

#### 1.判断类型
Isnumber():判断是否为数字

Iscolor():判断是否是颜色

Isurl():判断是否是路径

#### 2.颜色
saturate:增加一定数值的颜色饱和度

lighten:增加一定数值的颜色亮度

darken:降低一定数值的颜色亮度

fade:给颜色设置一定数值的透明度

mix:根据比例混合两种颜色

#### 3.数学函数
ceil、floor、round、sqrt、abs、pow
