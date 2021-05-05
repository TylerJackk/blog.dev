---
title: "Serverless爬虫(二)"
date: 2021-05-06:12:00+08:00
lastmod: 2021-05-06T00:12:00+08:00
draft: false
tags: ["Serverless", "Chalice", "AWS", "Lambda", "爬虫","Scraping","web crawling"]
categories: ["Tech"]
keywords: ["Serverless", "Chalice", "AWS", "Lambda", "爬虫","Scraping","web crawling"]
description: "基于AWS Lambda的Serverless爬虫-ETL"

weight: 10
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
## 背景
前一阵把爬虫部分迁移到了AWS Lambda上,这次把ETL部分代码也迁移过来.
目前已经迁移了全部历史数据,每天新增数据也会自动触发ETL任务,通过Kibana做了简单了数据分析和监控

## 架构
![Serverless ETL](https://raw.githubusercontent.com/TylerJackk/PicBed/master/blog_img/20210506012501.png)
ETL部分依赖存储**原始数据的S3**和用来**存储ETL任务的消息队列**

1. 完成对一部剧集的资源抓取后
   1. 原始数据存入S3
   2. 把对应源数据的S3路径发送到ETL消息队列
2. 处理ETL任务的Lambda Function监听ETL消息队列,当有新消息到达时
   1. 从S3中读取原始数据(Extract)
   2. 转换为符合ES Mapping的数据格式并补充剧集信息(Transform)
      1. 通过IMDB SDK获取中文标题和简介等
   3. 存储到对应的ES Index
      1. 数据量不大,简单根据类型进行Sharding(Movie,Series两个Index)



## 数据分析

#### 数据量

![数据量](https://raw.githubusercontent.com/TylerJackk/PicBed/master/blog_img/20210506013631.png)

目前抓到的数据中,Netflix所有区一共有大概16000部电影和电视剧等信息

#### 每日新增数量

拥有中文字幕(简体/繁体)的量级大概是5000,基本上就是香港区的资源量

![每日新增](https://raw.githubusercontent.com/TylerJackk/PicBed/master/blog_img/20210506014004.png)

每日新增的资源数量大概在个位数这个量级,考虑到影视资源的创作周期比较长,每天都能有新增的资源,离不开Netflix大量的合作和自制剧的退出,应该也在一直买一些资源的版权

#### 资源分布

![资源分布](https://raw.githubusercontent.com/TylerJackk/PicBed/master/blog_img/20210506014649.png)

资源分布的pie图,圆环内层是按国家和区域数据量的分布,外层是按电影和剧集的分布

先看外层基本和整体数据量一致,电影占大部分

内环的话可以看出大部分区域的资源量大致相同,资源丰富的区大概可以比少的区多出近1500部资源,有时候正是因为这些分布的不同导致想看的时候需要切换区域进行观看

#### 剧集类型

![剧集类型](https://raw.githubusercontent.com/TylerJackk/PicBed/master/blog_img/20210506015039.png)

Netflix对剧集的类型标签打的特别细,但是标签同时也是一对多的,意味着一部影片可以有很多类型标签

所以从pie图可以看出最多的top3类型就是:Drama(剧情),Comedies(喜剧),Action&Adventure(动作&冒险)