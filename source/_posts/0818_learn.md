---
title: 2017-08-18学习笔记
date: 2017-08-18
tags: [HashMap和HashTable的区别，LinkedHashMap，GC垃圾回收]
---

#### HashMap和HashTable的区别

* HashMap线程不安全；HashTable线程安全
    HashMap是非synchronized，而HashTable是synchronized，这就表示HashTable是线程安全的，多个线程可以共享一个HahTable；而对于HashMap来说，如果没有正确处理同步的话，多个线程是不能共享HashMap的。
但是Java5提供了ConcurrentHashMap，他是HashTable的替代，但是比HashTable的扩展性更强。
* HashMap允许有null的key、value；HashTable不允许有null的key、value
* HashMap的性能要高于HashTable
   由于HashMap不是线程安全的，HashTable是线程安全的，所以在单线程环境下HashMap的性能要高于HashTable。
* HashMap的方法不是Synchronize，HashTable的方法是Synchronize的
* HashMap有containsValue和containsKey方法；HashTable有contains方法
* HashMap是java1.2引进的Map interface的一个实现；HashTable继承于Dictionary类
* HashMap是HashTable的轻量级实现；HashTable比HashMap要早

#### 实现一个保证迭代顺序的HashMap

#### GC垃圾回收机制