---
title: "git仓库配置单独git config"
date: 2021-01-02T18:15:58+05:30
description: ""
tags: [git, Coding]
---
# 背景

平时使用git时默认用的是全局配置`.gitconfig`,当同一台电脑上有公司git和个人git时,都会默认使用这个配置,导致其中一个git的commit作者出错

# 使用

不同repo使用不同的git `user.name`和`user.email`

可以通过[Conditional includes](https://git-scm.com/docs/git-config#_conditional_includes)给不同目录的repo设置不同的`.gitconfig`

假设
* 公司repo都在目录`workspace`下

* 自己的repo都在`myspace`下,

* 全局`.gitconfig`配置的是公司git信息


创建一个新的`.gitconfig-myspace`来存储个人的git config
```
[user]
    name = name
    email = email@email.com
```
在全局配置`.gitconfig`里添加
```
[includeIf "gitdir:~/myspace/"]
    path = .gitconfig-myspace
```
这样`myspace`文件夹下的repo都是使用`.gitconfig-myspace`git信息

这样就可以区分公司和个人的git信息了