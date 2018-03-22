---
title: java基础面试题
date: 2018-03-22  
tags: [面试题,java]
---
1.Java中能创建volatile数组吗？
能，java可以创建volatile数组，不过只是一个指向数组的引用，而不是整个数组。如果改变引用指向的数组，将会受到volatile的保护，但是如果多个线程同时改变数组的元素，volatile标识符就不能气到之前的保护作用了

2.