---
title: "什么是Serverless"
date: 2021-02-21:00:00+08:00
lastmod: 2021-02-21:00:00+08:00
draft: false
tags: ["Serverless", "Chalice", "AWS", "Lambda"]
categories: ["Tech"]
keywords: ["Serverless", "Chalice", "AWS", "Lambda"]
description: "什么是Serverless"

weight: 10
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---

# Serverless

## 什么是Serverless

>Build and run applications without thinking about servers

>无服务器计算（或简称 serverless），是一种执行模型，在该模型中，云服务商（AWS，Azure 或 Google Cloud）负责通过动态分配资源来执行一段代码，并且仅收取运行代码所使用资源的费用。该代码通常运行在无状态的容器中，能够被包括 HTTP 请求、数据库事件、队列服务、监控报警、文件上传、调度事件（cron 任务）等各种事件触发。被发送到云服务商执行的代码通常是以函数的形式，因此，无服务器计算有时是指 “函数即服务” 或者 FAAS

Serverless也就是"无服务架构",是一种软件架构的方式和思路,核心是用户无需关心运行代码或应用程序的宿主(容器或服务器等)

不过Serverless的"无服务器"并不是真的不需要服务器,只是对普通用户来说,不需要再关心底层的服务器状态和资源(CPU,内存,磁盘,网络等)

传统的云服务器用户是在为**资源**付费(`硬件资源 * 时间`),是一种租赁行为,无论你在付费期间是否使用,价格都是一样的

而Serverless则是为**算力**付费(`(计算费用+请求费用) * 次数`),费用则是按需收取,大致上是根据计算的次数和每次计算消耗的资源收费

*<u>AWS Lambda的收费计算</u>*

![Serverless%20c197a9e7c21746a4bc2ff6d7ae6ec784/Untitled.png](Serverless%20c197a9e7c21746a4bc2ff6d7ae6ec784/Untitled.png)

## 优缺点

### 优点

- 无需管理服务器
  
  对开发人员来说此时已经没有服务器的概念,只需要关注自己的实现的代码逻辑
- 减少资源开销
  
  对于访问量不平均或者量级较小的应用,使用Serverless架构可以大大减少开销
- 减少运维成本
  
  运维工作交给了云服务厂商,一般来说会比开发人员要更专业更可靠
- 缩短交付周期
- [auto scale](https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html)
  
  Serverless架构一般都可以自动伸缩
- 开箱即用的运维工具
  
  Lambda提供CloudWatch和自带控制台等运维工具

### 缺点

- 启动延时
  处理请求的延时取决于当时是否有warm container,如果没有则会创建新的container

  ![Function生命周期](https://static001.infoq.cn/resource/image/a7/23/a745263102913ddcafff7d36fa298f23.png)
- 服务间延时
  
  serverless 应用程序是高度分布式、低耦合的,应用程序中不同组件的访问延迟是一个大问题
- 本地测试
- 状态管理
  
  lambda函数要尽可能保证无状态(stateless),会增加一些交互逻辑的复杂度
  >Q: Why must AWS Lambda functions be stateless?

  >Keeping functions stateless enables AWS Lambda to rapidly launch as many copies of the function as needed to scale to the rate of incoming events. While AWS Lambda’s programming model is stateless, your code can access stateful data by calling other web services, such as Amazon S3 or Amazon DynamoDB.
- 执行时间限制
  
  lambda目前单个执行时间限制为15min
- 对厂商的强依赖



目前看来Serverless的优缺点都还比较明显,但是根据这些优缺点来看,Serverless很适合一些个人的side-project,访问量不大,但是需要长期在线,如果买一台云服务器会比较浪费,但是如果选择用Serverless架构,厂商每个月的免费额度可能对我们来说都绰绰有余.
>AWS Lambda 免费使用套餐包含每月 1M 次免费请求以及每月 400000GB-秒的计算时间

此外一些爬虫或定时任务也很适合使用Serverless架构,假如我想每小时请求一个api获取比特币的当前价格,使用lambda一个月的成本几乎可以忽略,还省去了维护服务器的成本,只需要关注我的脚本即可



## Lambda的冷启动和无状态

## Reference

https://www.infoq.cn/article/SkLy3mGHNiKGVMVGXhT0

https://aws.amazon.com/cn/lambda/faqs/

https://www.infoq.cn/article/xqhoftxctpsk23rxkqoz

