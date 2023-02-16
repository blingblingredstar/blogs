---
title: '[JS全解]JS数据类型与运算'
date: 2019-09-16 11:59:01
categories: [学习笔记]
tags: [饥人谷, JavaScript, 数据类型]
---
> 本文章资料主要来自[饥人谷2020版前端体系课](https://mp.weixin.qq.com/s/nEzhoM75UCRMWReDEMnAfQ)，本文为课程笔记

## 数据类型

### 数字与字符串

+ 功能不同
  + 数字是数字，字符串是字符串
  + 数字可以进行加减乘除，字符串不可以
  + 字符串可以表示电话号码，数字不可以

+ 存储形式不同
  + JavaScript中，数字使用64位浮点数形式存储
  + 字符串使用类似UTF-8形式存储(UCS-2)
<!-- more -->
+ JavaScript如何存储数字
  + 十进制转为二进制
  + 用十六进制表示二进制
    + 为什么要用十六进制？
      + 二进制写起来太慢
        + 计算器中程序员模式可以转换各种进制
        + 其中HEX表示16进制，BIN表示2进制，OCT表示8进制，DEC表示10进制

+ JavaScript如何存储字符
  + 编号表示，用0-127表示所有符号，0为48号，A为65号，a为97号
  + 以上编号只能存储英文字符
  + 为了存储中文，推出中国国家标准局编号(国标2312)，但是此标准不包括生僻字、繁体字、韩文等
  + 微软为此推出一个扩展标准，简称GBK，兼容GB2312
  + 国标局后推出GB18030来取代GBK，但此标准不兼容GB2312
  + 但是GBK不包含泰文藏文等，为此推出万国码Unicode来解决所有需求

+ Unicode
  + 优点
    + 已收录13万字符(大于16位)，全世界通用
    + 可以继续扩充
  + 缺点
    + 每个字符需要三个及以上字节来存储
    + 增大了文件体积

+ UTF-8
  + 使用Unicode来表示字符，使用UTF-8来存储
  + UTF-8中8表示最少可用8位来存储一个字符

## JavaScript中的数据类型

+ 共七种
  1. Number
  2. String
  3. Boolean
  4. Symbol
  5. Null
  6. Undefined
  7. Object
     + 数组、函数、日期属于Object类型

### Number

+ JavaScript中使用64位浮点数存储数字
  + 浮点为小数点可以移动
    + 既123.456可以表示为1.23456e10^2
    + 也可表示为12345.6e10^-2
  + 64位存储一个数字
    + 符号1位
    + 指数11位(-1023 ~ 1024)
    + 有效数字52位(数字开头的1可以省略，使用科学计数法表示)

+ 范围
  + Number.MAX_VALUE: 1.7976931348623157e+308
  + Number.MIN_VALUE: 5e-324

+ 精度
  + 最多只能使用52+1个二进制位来表示有效数字
  + 也就是15位及以下有效数字都可以精确表示
  + 16位数字大于90开头，就不能有效表示(9110000000000001无法保存)

+ 写法
  + 整数写法
    + 1
  + 小数写法
    + 0.1
  + 科学计数法
    + 1.23e4
  + 八进制写法
    + 0123或00123或0o123
  + 十六进制写法
    + 0x3F或0X3F
  + 二进制写法
    + 0b11或0B11

+ 特殊值
  + +0与-0
  + Infinity、-Infinity、+Infinity
  + NaN

### String

+ 写法
  + 单引号
    + 'a'
  + 双引号
    + "a"
  + 模板字符串
    + \`a\`

+ 转义
  + 如果需要在字符串中输入特殊符号，使用`\`进行转义
    + \\uFFFF表示对应的Unicode字符
    + \\xFF表示前256个Unicode字符

+ 属性
  + string.length
    + '123'.length // 3
    + '\n\r\t'.length // 3
    + ''.length // 0
    + ' '.length // 1

+ 通过下标读取字符
  + string[index]
    + 下标从0开始计数

+ base64转码
  + `window.btoa`
    + 将字符串转为base64格式编码的字符串
  + `window.atob`
    + 将base64格式字符串转为原来的字符串
  + 可以用来隐藏招聘启事里的简历信息
  + 不要用于加密

### Boolean

+ 可以得出bool值的运算符
  + 否定运算
    + !
  + 相等运算
    + ==、!=、===、!==
  + 比较运算
    + \>、\>=、<、<=

+ falsy
  1. undefined
  2. null
  3. 0
  4. NaN
  5. ''

### undefined和null

+ 区别
  + 如果一个变量声明并没有赋值，那么会初始化为undefined
  + 如果一个函数没有显式的return，会默认return undefined
  + 习惯上将非对象空值写为undefined，对象空值写为null

### Symbol

+ 不常用

### 变量声明

+ 三种显式的声明方式
  + var
  + let
  + const

+ let
  + 规则
  + 创建新的块级作用域，既使用范围不会超出当前所在的{}
  + 不能重复声明
  + 可以赋值也可以不赋值
  + 必须先声明后使用(TDZ暂时性死区)
  + 使用let声明的全局变量，不会成为windows的属性
  + 与for循环配合使用时，有奇效

+ const
  + 声明时必须初始化，不能重新赋值
  + 其余与let一样

+ js中，变量没有类型，变量的值有类型

### 类型转换

+ Number => String
  + String(n)
  + n + ''
+ String => Number
  + Number(str)
  + parseInt(str)/paresFloat(str)
  + str - 0
  + +str
+ x => Boolean
  + Boolean(x)
  + !!x
+ x => String
  + String(x)
  + x.toString()
