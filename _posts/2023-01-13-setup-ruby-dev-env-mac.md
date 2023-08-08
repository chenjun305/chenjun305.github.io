---
layout: post
title: ruby开发环境搭建（mac）
date: 2023-01-13 17:00:00
description: 
tags: ruby
categories: programming
---
### 安装rbenv
* rbenv是ruby的多版本管理工具，方便在同一台电脑上安装多个不同的ruby版本
* https://github.com/rbenv/rbenv

```
$ brew install rbenv ruby-build
$ rbenv init
Load rbenv automatically by appending
the following to ~/.bash_profile:

eval "$(rbenv init - bash)"
```

### 安装ruby
* https://www.ruby-lang.org/

```
// list latest stable versions:
$ rbenv install -l

// install a Ruby version, rbenv将把ruby安装在~/.rbenv/目录下
$ rbenv install 3.1.3

// 设置全局默认的ruby版本，原理为通过这个文件控制~/.rbenv/version
$ rbenv global 3.1.3

$ rbenv versions
  system
* 3.1.3 (set by /Users/yueqi/.rbenv/version)

$ which ruby
/Users/yueqi/.rbenv/shims/ruby

// 设置单个项目使用的ruby版本，原理为通过将版本名写入当前项目目录下的.ruby-version文件实现
$ rbenv local 3.1.3
```

### RubyGems
* gem是ruby自带的第三方类库管理工具，从ruby 1.9版本开始，gem已经包含在ruby的安装包中。
* 查找，安装及发布gem: https://rubygems.org/ 

```
// 安装rails
$ gem install rails

// 列出已安装的gem
$ gem list
```

### Bundler
* 项目依赖管理工具，通过Gemfile管理项目的依赖
* Any modern distribution of Ruby comes with Bundler preinstalled by default.
* 如果没有跟随ruby安装，通过gem install bundler安装。
* https://bundler.io/
* Gemfile长这个样子

```
source 'https://rubygems.org'
gem 'jekyll'
gem 'rack', '~> 2.2.4'
gem 'rspec'
```
* 安装项目依赖

```
$ bundle install
$ git add Gemfile Gemfile.lock
```
* 执行命令

```
$ bundle exec jekyll serve
```