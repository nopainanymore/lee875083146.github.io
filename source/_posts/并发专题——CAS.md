---
title: 并发专题——CAS
tags: [并发,CAS]
categories:
  - Java
  - 并发
date: 2020-01-03 16:38:11
entitle: Java-Concurrency-CAS
---

CAS作为经典的无锁并发技术，很多底层的并发技术都依赖其实现。本文将介绍其思想以及在Java中的应用。

<!--more-->

## CAS设计思想

CAS是典型的乐观锁实现，乐观锁认为自己在使用数据的时候不会有其他的线程修改数据，在修改数据的时候判断是否有别的线程更新了数据，如果没有被修改则执行当前线程的写入或者更新操作，否则将根据不同的实现方式进行重试或者报错。

为什么会使用无锁呢？是因为悲观锁在使用时需要加锁、切换上下文、释放锁等操作，这些操作在锁竞争激烈的情况下，会导致CPU将大量的时间消耗在对锁的操作上，效率很低。

## 什么是CAS

CAS——Compare and Swap，顾名思义比较并交换。涉及到的参数有三个内存中的当前值，旧的期望值，想要设置的新值，只有在内存当前值与期望值相等时，才能用新值替换内存中的值。

当前的CPU提供CAS指令例如“cmpxchg”，是线程安全的。

## CAS在Java中的运用

CAS在Java中的实现依靠Unsafe类，其中包含以下三个方法。

```java
public final native boolean compareAndSwapObject(Object var1, long var2, Object var4, Object var5);

public final native boolean compareAndSwapInt(Object var1, long var2, int var4, int var5);

public final native boolean compareAndSwapLong(Object var1, long var2, long var4, long var6);
```

以`compareAndSwapInt(Object var1, long var2, int var4, int var5)`为例：

* var1：值所在的对象
* var2：内存偏移量
* var4：期望值
* var5：新值

该方法通过var1和var2定位并获取当前的内存值，与var4进行比较，如果相等则使用var5写入替换原值。

在Java中AtomicInteger应用了上述的方法实现了int线程安全的更新，常用来作为多线程中的计数。

```java
public final int incrementAndGet() {
    return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
}
```

```java
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

    return var5;
}
```



## CAS面临的问题

### 自旋


### ABA



## 参考资料
<https://tech.meituan.com/2018/11/15/java-lock.html>
<https://blog.biezhi.me/2019/01/head-first-cas.html>
<https://juejin.im/post/5c021da16fb9a049e65ffcbf>
<https://juejin.im/post/5c87afa06fb9a049f1550b04>
