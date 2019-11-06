---
title: 并发专题——Java并发基础
tags: [并发,Java,Runnable,Thread,Future,CompletableFuture]
categories:
- Java
- Future
date: 2019-11-01 16:37:28
entitle: Concurrency-Java-Basics
---

本文介绍Java并发、多线程的一些基础概念。

<!--more-->

## Runnable、Thread、Callable

在Java中实现线程任务有三种方式：
* 实现`Runnable`接口
* 继承`Thread`类
* 实现`Callable`接口

`Runnable`、`Thread`两者都定义了`void`类型的`run`方法，而`Callable`则使用范型定义了带返回类型的`call`方法。

由于Java是单继承的，使用继承Thread类去实现线程是十分局限的。同时，如果遵从好的设计实践的话，继承意味着扩展父类的功能，但当创建线程的时候，并没有扩展Thread类的功能，仅仅只需要提供run方法的实现即可。所以应该始终使用Runnable对象创建线程，这种实现方式更加灵活。并且允许你的类继承自其他类。

使用Thread提供的sleep方法可以暂停当前执行的过程一段具体时间。如果当前线程被其他线程中断，sleep方法会抛出一个已检查异常——InterruptedException。

使用Runnable有一个缺陷就是无法返回执行的结果，Callable接口定义了带返回值的任务，


## ExecutorService、ThreadPool

为了资源的复用、提高响应速度以及方便的管理线程，Executor框架应运而生，该框架将线程的创建和管理与应用分离开来。

Executor框架的主要作用：
* 线程创建：使用线程池创建线程
* 线程管理：管理线程的生命周期，调用者不需要关心线程的状态
* 任务提交和执行：Executor框架提供了提交任务给线程池的方法，同时给予你决定任务何时被执行的权利，即你可以决定提交的任务立即被执行或者安排任务延迟或者定期执行。

JavaJCU包中提供了三个Executor接口，提供了创建和管理线程的所有方面：
* Executor：包含了一个execute方法的接口，该方法通过接受一个Runnable对象来启动一个任务
* ExecutorService：是Executor的子接口，添加了一些功能去管理任务的生命周期。同时提供了submit方法，通过重载该方法接受Runnable和Callable的对象。
* ScheduledExecutorService：是ExecutorService的子接口，添加了定时执行任务的功能。

ExecutorService可以通过以下两个方法关闭：
* shutDown：当该方法被调用后，ExecutorService将停止接受新的任务，当当前执行的任务全部结束后，停止当前的Executor
* shutDownNow：当该方法被调用后，将会中断当前正在执行的任务并且立即关闭Executor

大多数的Executor实现都使用线程池来执行。线程池包含了

## Future


## CompletableFuture



在Java并发中，我们通常使用ExecutorService线程池，通过submit方法提交任务给线程池，该方法接受 runnable、Callable的参数。

任务的实现方式有三种，实现Runnable、继承Thread、实现Callable



## 参考资料
<https://www.callicoder.com/java-callable-and-future-tutorial/>
<https://juejin.im/post/5adbf8226fb9a07aac240a67>
