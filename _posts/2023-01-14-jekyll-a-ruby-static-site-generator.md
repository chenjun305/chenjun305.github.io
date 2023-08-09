---
layout: post
title: jekyll - a ruby static site generator
date: 2023-01-14 17:00:00
description: 
tags: ruby
categories: programming
---
### jekyll简介
* 官网：[https://jekyllrb.com/](https://jekyllrb.com/)
* jekyll是一个静态网站生成器，用户不需要使用html/css/js，通过markdown就可以编写网站，jekyll会将其转化为静态网页。
* GitHub pages官方支持的工具
* 发展时间较长，社区有大量的theme可以直接免费使用。
* jekyll是使用ruby开发的工具，其他语言的类似工具，比较有名的有golang开发的HUGO：[https://gohugo.io/](https://gohugo.io/)


### 如何使用
* 由于jekyll是使用ruby开发的，所以需要先有ruby环境。
* jekyll是ruby的一个gem, 通过gem install就可以安装。

```
$ gem install bundler jekyll
$ jekyll new my-site
$ cd my-site
$ bundle exec jekyll serve

// 打开浏览器访问 http://localhost:4000

```

### 常用命令
* jekyll new PATH : 创建新项目
* jekyll build : 生成网站静态文件到_site目录下
* jekyll serve : 发布网站在本地，通过localhost:4000访问

### 在github pages发布
* 可通过配置github action的workflow实现网站自动更新。