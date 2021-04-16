---
title: "Python Built-in Data Structures Time Complexity"
date: 2021-04-16:00:00+08:00
lastmod: 2021-04-16T00:00:00+08:00
draft: false
tags: ["coding"]
categories: ["Tech"]
keywords: ["Python", "Built-in Data Structures", "Time Complexity"]
description: "Python Built-in Data Structures Time Complexity"

weight: 10
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---

# List

|                 Method                 | Time Complexity |
| :------------------------------------: | :-------------: |
|                list[i]                 |      O(1)       |
|              list[i] = x               |      O(1)       |
|             list.append()              |      O(1)       |
| list.insert(i,x) <br />#insert on tail |      O(1)       |
|            list.insert(i,x)            |      O(n)       |
|             list.append()              |      O(1)       |
|      list.pop()<br />#pop on tail      |      O(1)       |
|    list.pop(i)<br /># pop on index     |      O(n)       |
|               xx in list               |      O(n)       |
|             list.reverse()             |      O(n)       |
|        min(list)<br />max(list)        |      O(n)       |
|               len(list)                |      O(1)       |
|               list[x: y]               |    O(y - x )    |


# Dict

|      Method       | Average Time Complexity | Worst |
| :---------------: | :---------------------: | :---: |
|    key in dict    |          O(1)           | O(n)  |
|     dict[key]     |          O(1)           | O(n)  |
| dict[key] = value |          O(1)           | O(n)  |
|   dict.pop(key)   |          O(1)           | O(n)  |