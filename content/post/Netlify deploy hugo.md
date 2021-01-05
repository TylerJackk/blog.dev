---
title: "Netify deploy Hugo version error"
date: 2021-01-05:00:00+08:00
lastmod: 2021-01-05T00:00:00+08:00
draft: false
tags: ["hugo", "blog"]
categories: ["Tech"]

weight: 10
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---

# Hugo version error

`EVEN theme does not support Hugo version 0.54.0. Minimum version required is 0.60.0`

```shell
6:11:34 PM: $ hugo
6:11:34 PM: ERROR 2021/01/04 10:11:34 EVEN theme does not support Hugo version 0.54.0. Minimum version required is 0.60.0
6:11:34 PM: Building sites … Total in 205 ms
6:11:34 PM: Error: Error building site: logged 1 error(s)
6:11:34 PM: 
6:11:34 PM: ────────────────────────────────────────────────────────────────
6:11:34 PM:   "build.command" failed                                        
6:11:34 PM: ────────────────────────────────────────────────────────────────
6:11:34 PM: 
6:11:34 PM:   Error message
6:11:34 PM:   Command failed with exit code 255: hugo
6:11:34 PM: 
6:11:34 PM:   Error location
6:11:34 PM:   In Build command from Netlify app:
6:11:34 PM:   hugo
6:11:34 PM: 
6:11:34 PM:   Resolved config
6:11:34 PM:   build:
6:11:34 PM:     command: hugo
6:11:34 PM:     commandOrigin: ui
6:11:34 PM:     publish: /opt/build/repo/public
```

## solution

在netlify进行部署的项目设置中设置Hugo version即可

具体在 Environment → Environment → variables

添加`HUGO_VERSION` key, value为最新或高于你使用皮肤所要求版本的任一版本即可

![](https://raw.githubusercontent.com/TylerJackk/PicBed/master/blog_img/20210105221455.png)

Hugo版本号可参考[GitHub release](https://github.com/gohugoio/hugo/releases)


# git submodule error

`Error checking out submodules: fatal: No url found for submodule path`

```shell
5:15:58 PM: Error checking out submodules: fatal: No url found for submodule path 'themes/even' in .gitmodules
5:15:58 PM: Failing build: Failed to prepare repo
5:15:58 PM: Failed during stage 'preparing repo': Error checking out submodules: fatal: No url found for submodule path 'themes/even' in .gitmodules
: exit status 128
```

## solution

删除`themes/your-theme`目录下的`.git`目录

`rm -rf themes/your-theme/.git/`