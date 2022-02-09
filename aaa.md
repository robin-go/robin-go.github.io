---
title: redis跳跃表
date: 2022-02-09 00:02:34
tags: redis设计与实现学习笔记
---

### **跳跃表**

跳跃表节点查找的时间复杂度 O(logN)

大部分情况下跳跃表的效率可以和平衡树媲美，跳跃表实现更简单，且支持范围查询

跳跃表是zset的实现之一(集合包含元素较少多，或者字符串较长时)

redis跳跃表由`zskiplistNode` 和 `zskiplist` 实现

```go
typedef struct zskiplistNode {
    sds ele;                          //sds 字符串
    double score;                     //分值 
    struct zskiplistNode *backward;    //后退指针，每次只能退一个节点
    struct zskiplistLevel {            
        struct zskiplistNode *forward; //每层都有一个前进指针，层数越高，跨度越大
        unsigned long span;            //跨度记录两个节点之间的距离
    } level[];                         //层高在1-32之间，根据幂次定律，越大的数出现的概率越小
} zskiplistNode;

typedef struct zskiplist {
    struct zskiplistNode *header, *tail;
    unsigned long length;
    int level;
} zskiplist;

typedef struct zset {
    dict *dict;
    zskiplist *zsl;
} zset;
```
