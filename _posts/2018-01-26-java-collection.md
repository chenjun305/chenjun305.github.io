---
layout: post
title:  Java常用容器类总结
date:   2018-01-26 15:14:10
description: 
tags: java
categories: programming
---

# Collection
## List 
* ArrayList 数组 长于随机访问元素，但在List中间插入和移除元素时较慢
* LinkedList 链表 随机访问元素比较慢，中间插入和移除元素时较快，优化的顺序访问，特性集比ArrayList更大

## Set 
相同的项只保存1次
* HashSet 使用相当复杂的方式来存储元素，这种技术是最快的获取元素的方式。
* TreeSet 按照比较结果的升序保存对象
* LinkedHashSet 按照添加的顺序保存对象
  
HashSet使用的是散列函数，TreeSet将元素存储在红-黑树数据结构中，LinkedHashSet因为查询数据的原因也使用了散列，且使用了链表来维护元素的插入顺序。

# Map 
键值对，不必制定尺寸，自动调整尺寸。
* HashMap 提供了最快的查找速度，没有按照明显的顺序来保存元素。
*  TreeMap 按照比较结果的升序保存键
*  LinkedHashMap 按照插入顺序保存键， 同时保留了HashMap的查询速度