---
title: javaScrip基础实践（一）
# categories: 技术分享 #文章分类目录 可以省略
tags: javaScript
description: javaScrip基础实践
date: 2019-11-30 16:31:34
---

## javaScrip基础实践及代码编写风格规范

### 一、位元运算符
1. 基础介绍，计算机用二进制表示数字，8个位（bit，简写为b）组成一个字节（Byte，简写为B），是最小一级的信息单位。比如：
```
// 1 = 00000001
// 2 = 00000010
```
<!-- more -->
2. javaScript的位元运算类似逻辑运算，他们作用于每个独特的bit;
```
// 1 = 00000001
// 2 = 00000010
// OR = 00000011
// AND = 00000000


console.log(1 | 2) // 位元运算符或OR  ==> 3

console.log(1 & 2) // 位元运算符或AND  ==> 0

```
3. 实际运用-权限控制：
假如用户有三种权限，读取、写入、操作
```
// Read、Write、Execute
// 00000100 代表有读取权限
// 00000010 代表有写入权限
// 00000001 代表有操作权限
```
用位运算符来处理权限:
```
const readPermission = 4;
const writePermission = 2;
const executePermission = 1;
let myPermission = 0;
myPermission = myPermission | writePermission;
let message = (myPermission & readPermssion) ? 'yes' : 'no';
console.log(message) // no
```

### 二、FizzBuzz问题
写一个方法打印1到100这些数字。但是遇到数字为3的倍数的时候，打印“Fizz”替代数字，5的倍数用“Buzz”代替，既是3的倍数又是5的倍数打印“FizzBuzz”。
```
for(let i=0;i<100;i++){
    console.log(fizzBuzz(i))
}
function fizzBuzz(input) {
    if(typeof input !== 'number' || isNaN(input)) return 'Not a number'
    if(input % 3 ===0 && input % 5 === 0) return 'FizzBuzz'
    if(input % 3 ===0) return 'Fizz'
    if(input % 5 ===0) return 'Buzz'
    return input
}
```
主要在函数内更早的将不同的条件排除或者return出去，方便后续业务功能的维护。

### 三、编码风格
写一个方法，传入一个数字，获得0到该数字之内所有被3或5整除之和；
```
console.log(sum(10))
function sum(limit) {
    let sum = 0;

    for(let i = 1; i <= limit; i++ )
        if(i % 3 === 0 || i % 5 === 0)
            sum += i

    return sum
}
```
用空行将变量初始化、代码逻辑、返回部分分开，便于阅读，高度相关的代码应该紧靠在一起，与别的不相干代码分开；