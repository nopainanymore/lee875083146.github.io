---
title: Java8Stream介绍
entitle: Java8-Stream-Introduction
date: 2019-03-16 02:29:38
tags: [Java,Java8,Stream]
categories:
- Java
- Java8
- Stream
---

Java 8中的Stream是对集合对象功能的增强，Stream专注于对集合对象进行各种非常便利、高效的聚合操作，或者大批量数据操作。Stream API 借助Lambda表达式，极大的提升编程效率及程序的可读性。同时Stream提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势，使用 fork/join 并行方式来拆分任务和加速处理过程。
<!--more-->
# Stream介绍

## 概念
Stream 并不是某种数据结构，它只是数据源的一种视图，是对集合进行操作的函数管道。

## 特点及优点

### 特点
* 无存储：stream不是一种数据结构，它只是某种数据源的一个视图，数据源可以是一个数组，Java容器或I/O channel等。
* 功能性：对流进行操作产生一个结果，不会修改源，比如对stream执行filter并不会删除被过滤的元素，而是会产生一个不包含被过滤元素的新stream。
* 惰式执行：stream上的操作并不会立即执行，只有等到用户真正需要结果的时候才会执行。
* 可消费性：stream只能被“消费”一次，一旦遍历过就会失效，就像容器的迭代器那样，想要再次遍历必须重新生成。

### 优点
* Stream简洁优雅，代码易读。
* Stream采用惰性计算，使程序非常高效。
* Stream支持并行，只需调用 `parallel()`方法。

## 创建
* 调用`Collection.stream()`或者`Collection.parallelStream()`方法
* 调用`Arrays.stream(T[] array)`方法
* Stream类的静态工厂方法`Stream.of(Object[]), IntStream.range(int, int)， Stream.iterate(Object, UnaryOperator);`

## 操作
对Stream的操作分为两类，中间操作(intermediate operations)和结束操作(terminal operations)
1. 中间操作总是会惰式执行，调用中间操作只会生成一个标记了该操作的新stream。
2. 结束操作会触发实际计算，计算发生时会把所有中间操作积攒的操作以pipeline的方式执行，这样可以减少迭代次数。计算完成之后stream就会失效。

* 中间操作

类别|含义|操作
:-:|:-:|:-
无状态(Stateless)|每个数据的处理是独立的，不会影响或依赖之前的数据|`filter()、flatMap()、flatMapToDouble()、flatMapToInt()、flatMapToLong()、map()、mapToDouble()、mapToInt()、mapToLong()、peek()、unordered()`等
有状态(Stateful)|处理时会记录状态|`distinct()、sorted()、sorted(comparator)、limit()、skip()` 等

* 终止操作

类别|含义|操作
:-:|:-:|:-
非短路|处理完所以数据才能得到结果|`collect()、count()、forEach()、forEachOrdered()、max()、min()、reduce()、toArray()`等
短路|拿到符合预期的结果就会停下来，不一定会处理完所有数据|`anyMatch()、allMatch()、noneMatch()、findFirst()、findAny() `等

## 惰性计算

Stream累积并组合或融合中间操作，然后执行他们。Stream不会像命令式代码一样一个一个执行数据集上的每个函数，而是执行每个元素上的函数的融合集合，仅在需要时执行。

## 函数接口及函数纯度

Stream集合管道中的所有lambda表达式和闭包都必须是纯的。

### 纯函数

纯函数是幂等的— 这意味着对纯函数的调用次数没有限制。其次，无论调用纯函数多少次，只要给定相同的输入，它都会产生相同的结果。第三，纯函数没有副作用：无论您使用它做什么，纯函数都不会更改您的程序中的其他任何元素。
规则：
* 函数不会更改任何元素
* 函数不依赖于任何可能更改的元素

### Stream中

* 无干扰(non-interfering)：该方法不修改stream的底层数据源。
* 无状态(stateless)：操作的执行是独立的，没有lambda表达式在执行中更改或者依赖依赖可能发生变化的外部变量或状态。

# 参考资料
[Java 8 中的 Streams API 详解](https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/)
[Java 8 Stream 教程](https://www.jianshu.com/p/0c07597d8311)
[函数纯度](https://www.ibm.com/developerworks/cn/java/j-java8idioms11/index.html)
[Java 8 Stream探秘](https://colobu.com/2014/11/18/Java-8-Stream/)
[JavaLambdaInternals](https://github.com/CarpenterLee/JavaLambdaInternals)
