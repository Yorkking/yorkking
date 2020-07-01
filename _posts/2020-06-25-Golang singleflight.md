---
title: singleflight
tags:
 - Go
---

缓存更新问题：如何进行缓存更新。

服务中某个接口请求量暴增问题。

groupcache 缓存更新能够做到对同一个失效 key 的**多个请求**，**只有一个请求**指向对 key 的**更新**操作。

即多个线程并发访问时执行某个函数时，只有一个线程会执行该函数，其他的线程只是得到返回结果，并不执行。

singleflight 是如何做到这一点的：

call的一个数据结构

```Go
type call struct{
    wg sync.WaitGroup
    val interface{}
    err error
}
```

Group 可以看成任务分类的数据结构

```Go
type Group struct{
    m sync.Mutex
    m map[string]*call
}
```



我发现我对 Go 的协程的理解还是不太够；

特别是它的 mutex 到底是怎样实现的？难道不是共享内存吗？

