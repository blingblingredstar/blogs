---
title: '[HTTP全解]Node.js Server入门'
date: 2019-08-30 10:23:19
categories: [学习笔记]
tags: [饥人谷, HTTP, Node.js, curl]
---
本文章资料主要来自[饥人谷2020版前端体系课](https://mp.weixin.qq.com/s/nEzhoM75UCRMWReDEMnAfQ)，本文为课程笔记。

## 请求与响应模型

请求(request)一般由客户端发起，服务器收到后，返回一个响应(response)。

### 如何发起请求

1. 使用浏览器
2. 使用命令行(curl命令)
   + 参考资料 [阮一峰-curl网站开发指南](http://www.ruanyifeng.com/blog/2011/09/curl.html)

发送请求的工具就是用户代理(User Agent)。
<!-- more -->
### 如何进行响应

这里使用Node.js。

我们在服务器可以使用Node.js监听一个端口，当有请求发送到端口时，就可以执行预先写好的代码。

一个简单的实现就是通过条件判断路径，根据不同路径做出不同响应。注意收到请求后一定要返回一个响应。

我们通过`response.setHeader`设置响应头(Response Headers)来指定文件类型(Content-Type)

通过`response.write`来返回一个响应体。

通过`response.end`来结束响应。

## HTTP

### 什么是HTTP

+ 复习体系化学习方式
  + 必学知识
    + 基础概念
    + 如何调试(Node.js中可以使用log/debugger/Chrome inspect)
    + 权威资料(Node.js文档)
    + 标准制定者(HTTP规格文档: RFC 2612)
  + 如何学习
    + Copy
    + Modify
    + Run

### HTTP基础概念

+ 请求
  + **请求行**
    + 请求动词 + 路径与查询参数 + 协议名/版本
      + 动词有GET/POST/PUT/PATCH/DELETE等
  + 请求头(Requset Headers):
    + Host: 域名/IP
    + Accept: text/html
    + Content-Type: 请求体格式
  + 换行符
  + 请求体
    + 在GET请求中一般为空
  + 注意
    + 分为三部分:
      1. 请求行
      2. 请求头
      3. 请求体
    + 文档位于RFC 2612第五章
    + 大小写不敏感(case insensitive)

+ 响应
  + **状态行**
    + 协议名/版本 + 状态码 + 状态字符串
  + 响应头
    + Content-Type: 响应体格式
  + 换行符
  + 响应体
  + 文档位于RFC 2612第六章

### 如何用curl构造请求

+ 基本用法
  + `curl -v http://localhost:8888`
+ 设置请求动词
  + `curl -v -X POST http://localhost:8888`
+ 添加路径和查询参数
  + `curl -v http://localhost:8888/a?wd=2`
+ 设置请求头
  + `curl -v http://localhost:8888 -H 'Name: Value'`
+ 设置请求体
  + `curl -v http://localhost:8888 -d '内容'`

### 使用Node.js获取请求

+ 获取请求动词
  + `request.method`
+ 获取请求路径
  + `request.url`，含有查询参数
  + `request.path`，不含查询参数
  + `request.query`，只有查询参数
+ 获取请求头
  + `request.headers[请求头属性]`
+ 获取请求体

### 使用Node.js响应请求

+ 设置响应状态码
  + `response.statusCode`
+ 设置响应头
  + `response.setHeader('Content-Type', 'text/html')`
+ 设置响应体
  + `response.write('内容’)`
  + 可追加内容
