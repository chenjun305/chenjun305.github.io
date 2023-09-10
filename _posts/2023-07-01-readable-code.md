---
layout: post
title: 如何提高代码可读性
date: 2023-07-01
description: 
tags: architecture
categories: programming
---
先介绍一本好书

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/readable-code-book-english.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/readable-code-book-chinese.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    编写可读代码的艺术
</div>

以下代码可读性好吗？

```
if ( !isNotEmpty(str) ) {

} else {
    do();
}
```

这样写是不是更好？

```
if ( isEmpty(str) ) {

} else {
    do();
}

```

### 高可读性代码定义
* 定义：使别人用最短的时间理解你的代码
* 这里的别人也包括1个月后的自己
* 不给别人挖坑=不给自己挖坑
* 不要让别人看到我们的代码一脸懵逼

### 高可读性代码的4个要素
* 命名
* 注释
* 简化循环和控制逻辑
* 重构你的代码

#### 命名
* 包括类名，方法名，变量名，常量名等等
* 达到见名知意
* 使用明确的，正确的，常用的简单英语单词
  * Bespeak是什么意思？（尽量不要使用不常用的单词）
  * 90%的情况下高中为止的单词就足够了

##### 使用明确，正确，简单的单词
* 从外部加载页面
  * `Page getPage() {` : 不够准确
  * `Page loadPage() {` : 更准确一些
* 通过名字检索
  * `List<Person> getByName(String name) {` : 不够准确，get方法一般只会返回1个对象
  * `List<Person> findByName(String name) {` : 更准确一些

##### 使用更具体的名称
* 把Date转化为yyyyMMdd形式的字符串
  * `String toDateString(Date date)` ： 不够具体
  * `String toYYYYMMDD(Date date)` ： 更具体，基本上见名知意
* 字符串列表中返回符合条件的下标列表
  * 写法1:
```
List<Integer> result = new ArrayList<>();
 //……
 return result;
```
  * 写法2:
```
️List<Integer> matchedIndexes = new ArrayList<>();
 //……
 return matchedIndexes;
```
  * 上面两种写法中，变量名matchedIndexes取得更具体，理解成本更低。

##### 什么时候可以缩写/省略
* 不可以省略的情况
  * `class WDate {}` : 这里把War缩写成W就不知道什么意思了
  * `class WarDate {}`
* 可以省略的情况
  * `Document doc = new Document();` : document缩写成doc基本不影响理解，而且基本是业界约定俗成的缩写
  * `Document document = new Document();`
  * `Date convertToDate(String dateExpression, String datePattern);`
  * `Date toDate(String dateExpression, String datePattern);` ： 去除convert这个单词也不影响意思表达
* 这里需要注意的是，命名并不是越长越好，名字太长，也会增加读者理解成本

##### 灵活使用前缀和后缀
* 检查文件大小
  * `public boolean isFileSizeOver(File file, int size)`
  * `public boolean isFileSizeOver(File file, int size_kb)` : 这里的参数名size_kb很好地传达了单位是kb, 可以使读者更好地理解方法的意图。
* boolean型变量或方法，使用is, has, can, should等前缀
  * `if ( user.auth() ) {`
  * ` if ( user.hasAuthority() )` :  有has前缀，很容易理解该方法返回值类型为boolean

##### 命名时的要点
* 见名知意
* 从多个候选单词中找出一个最正确，最明确的
* 选一个最不会引起误解的单词
* 参考其他同学的意见
* 要有工匠精神，精益求精

#### 注释
首先，我并不认为注释越多越好，长篇大论，同样也会增加阅读成本，浪费读者的时间。  
不要写没有价值的注释，看代码就明白什么意思的情况不要写注释.  
```
//通过名字检索
  List<Person> findByName(String name) {
```
```
//遍历array数组
  For (int i=0; i<array.count; i++) {
```
上面两个例子中的注释，是没有必要的，因为看代码其实已经理解意思了。这样的注释只是增加了代码行数。（除非你所在的公司会考核工程师的代码行数）

##### 什么时候应该写注释
* 常量（magic number)的背景
* 有可能让人产生疑问的地方
* BUG／踩过的坑的注释
* 复杂的或独特的功能或算法
* 注释应该与代码行为一致

#### 简化循环和控制逻辑
这里主要是要减少理解的曲折度，能直着说就别绕着说，减少理解代码所需的脑容量。
* 变化的放在左边
  * `if(10<length)`
  * `if (length > 10)` : 把变量放到左边更符合程序员的思维习惯
* if-else尽量把肯定排在前面
* 循环层次不可太深，最好不要超过3层
* 循环时break, continue尽早返回
* if-else条件语句尽量把肯定放在前面
  * 写法1
  ```
  if ( ! user.hasAuthority(“admin”) ) {
    // ..
  } else {
    // ..
  }
  ```
  * 写法2
  ```
  if ( user.hasAuthority(“admin”) ) {
    // ..
  } else {
    // ..
  }
  ```
  写法2，if条件是肯定语句，更好理解一些。

#### 重构代码
* 列出所有任务并把它们分开
* 一个方法只完成1个任务
* 当1个类上千行或者1个方法上百行时，通常意味着应该把他们分成不同的类或方法
* 持续重构，持续改进
