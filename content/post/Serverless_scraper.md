---
title: "Serverless爬虫(一)"
date: 2021-03-27:12:00+08:00
lastmod: 2021-03-27T00:12:00+08:00
draft: false
tags: ["Serverless", "Chalice", "AWS", "Lambda", "爬虫","Scraping","web crawling"]
categories: ["Tech"]
keywords: ["Serverless", "Chalice", "AWS", "Lambda", "爬虫","Scraping","web crawling"]
description: "基于AWS Lambda的Serverless爬虫"

weight: 10
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
## 背景
之前自己写了一个爬虫,爬取[uNoGS](https://unogs.com/)网站的Netflix资源信息(哪个区可看,字幕和配音信息等),通过一些影视Sdk补充资源完整度并存储到ES中来提供查询服务

前一阵了解了一些Serverless的概念和Chalice框架的一些细节,发现很适合把项目转移到Serverless架构上

### Before
* 代码运行在EC2 Instacne的docker中,爬取的原始数据存储在S3,ETL完成后的数据存在AWS ES中
* 小项目自己没有配置对应的CI/CD,每次代码改动后需要人工登录EC2机器拉取代码并重新启动
* 爬虫运行状况缺少监控和报警等,比较依赖人工检查
  
### After
* 把所有代码转为Serverless Function,通过消息队列解耦(SQS)
* AWS Lambda Function和AWS其他功能有较好的联动(多种Trigger)
* Lambda自带监控和日志,方便监控运行状态
* 调试和部署非常方便

## 架构
![Serverless爬虫](https://raw.githubusercontent.com/TylerJackk/PicBed/master/blog_img/20210327221814.png)

具体拆分了以下几个模块
* Scraping
    * 项目初始时的全量抓取
    * daily job检查新加入的资源
    * 全部原始数据简单整合后存入S3
* ETL
    * 从S3读取数据
    * 通过[TMDB](https://www.themoviedb.org/) SDK汉化数据并补充信息
    * 转为符合ElasticSearch Mapping的数据并存储
* Backend Service
    * 提供根据各种维度的查询和搜索
    * 对外暴露restful Api
* Frontend
    * PWA Application,全平台可用

## Scraping
目前这部分已经完全重构并迁移到AWS Lambda上,已正常运行
截止目前抓取Netflix资源信息
```
电影6300条
电视剧综艺等2200条
每日新增资源5条左右
```

*Scraping*模块主要用到了以下资源
* **EC2 Instance**
    * 由于lambda函数单次执行时间最大为15min,所以全量抓取时无法向以前一样循环抓取所有数据
    * 在项目初始时在机器上起一个脚本循环调用lambda function去抓取所有数据,单次函数调用控制在15分钟内即刻
* **Lambda Function**
  
    爬虫按角色分为以下3种
    * **Explorer**
        * 分为全量检查和每日检查,查找要爬取的资源id(netflix_id)并加入到消息队列(SQS)
        * 每条消息最多50个id,保证抓取执行时间在15分钟以内
    * **Scraper**
        * 通过监听指定消息队列,队列有新消息时自动启动
        * 被消息启动后抓取资源id对应的全部具体信息
            * 资源详情(名,描述,国家,上映日期等)
            * 图片等
            * cast
            * netflix各区上映情况,不同区字幕和音频情况
        * 简单整合后存储到S3
    * **StaticScraper**
        * 抓取Netflix总体静态信息
        * 目前支持多少个国家,每个国家的资源数据量(对应的Code)
        * 影视类型的`name-code mapping`
        * 所有语言和字幕的`name-code mapping`

## Todo
下一步把ETL部分也迁移到Serverless上


