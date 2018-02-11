---
title: LinkedHashMap 底层分析
date: 2018-02-08 09:40:50
categories: java
tags:
    - Java
    - HashMap
    - LinkedHash
    - jdk
---
  众所周知HashMap是一个无顺序的Map,因为每次根据key的hashcode映射到Entry
数组上，所以遍历出来的顺序并不是写入的顺序。
  
因此JDK退出一个机遇HashMap但具有顺序的LinkedHashMap来解决有排序需求的场景。
它的底层是继承于HashMap实现的，有一个双向链表所构成。
LinkedHashMap的排序方式有两种：
1.根据写入的顺序排序。
2.根据访问的顺序排序。
其中根据访问顺讯排序时，每次get都会将访问的值移动到链表末尾，这样操作就能
得到一个按照访问顺序的链表。
数据结构
```
@Test
public void test(){
    Map<String,Integer> map = new LinkedHashMap<>();
    map.put("1",1);
    map.put("2",2);
    map.put("3",3);
    map.put("4",4);
    map.put("5",5);
    System.out.println(map.toString());
}
```
调试可以看到map的组成。
---capture pic

打开源码可以看到：
```
    /**
     * The head of the doubly linked list.
     */
    private transient Entry<K,V> header;

    /**
     * The iteration ordering method for this linked hash map: <tt>true</tt>
     * for access-order, <tt>false</tt> for insertion-order.
     *
     * @serial
     */
    private final boolean accessOrder;
    
    private static class Entry<K,V> extends HashMap.Entry<K,V> {
        // These fields comprise the doubly linked list used for iteration.
        Entry<K,V> before, after;

        Entry(int hash, K key, V value, HashMap.Entry<K,V> next) {
            super(hash, key, value, next);
        }
    }
```
其中Entry继承于HashMap的Entry，并新增上下节点的指针，也就形成了双向链表。
还有一个header的成员变量，是这个双向链表的头节点。
上面demo总计程一张图如下：
----capture pic

第一个类似于HshMap的结构。
  
  
<!-- more -->
