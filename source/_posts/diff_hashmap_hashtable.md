---
title: HashMap和HashTable的区别
date: 2017-08-18
tags: [HashMap，HashTable，LinkedHashMap，GC]
---

#### HashMap和HashTable的区别

一、 HashMap线程不安全；HashTable线程安全
    HashMap是非synchronized，而HashTable是synchronized，这就表示HashTable是线程安全的，多个线程可以共享一个HahTable；而对于HashMap来说，如果没有正确处理同步的话，多个线程是不能共享HashMap的。
但是Java5提供了ConcurrentHashMap，他是HashTable的替代，但是比HashTable的扩展性更强。

二、 HashMap允许有null的key、value；HashTable不允许有null的key、value

<!-- more -->

三、HashMap的性能要高于HashTable
   由于HashMap不是线程安全的，HashTable是线程安全的，所以在单线程环境下HashMap的性能要高于HashTable。
   
四、 HashMap的方法不是Synchronize，HashTable的方法是Synchronize的

五、 HashMap有containsValue和containsKey方法；HashTable有contains方法

六、 HashMap是java1.2引进的Map interface的一个实现；HashTable继承于Dictionary类

七、 HashMap是HashTable的轻量级实现；HashTable比HashMap要早

#### 实现一个保证迭代顺序的HashMap
我们都知道HashMap的存放是无序，但是我们又想要保证迭代顺序的HashMap；这时我们可以使用LinkedHashMap。
LinkedHashMap<K,V> 接口的哈希表和链接列表实现，具有可预知的迭代顺序。
此实现与 HashMap 的不同之处在于，后者维护着一个运行于所有条目的双重链接列表。

备注：如果在映射中重新插入键，则插入顺序不受影响

#### GC垃圾回收机制