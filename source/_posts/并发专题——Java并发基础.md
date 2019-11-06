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

由于Java是单继承的，使用继承`Thread`类去实现线程是十分局限的。同时，如果遵从好的设计实践的话，继承意味着扩展父类的功能，但当创建线程的时候，并没有扩展Thread类的功能，仅仅只需要提供`run`方法的实现即可。所以应该始终使用`Runnable`对象创建线程，这种实现方式更加灵活。并且允许你的类继承自其他类。

使用`Thread`提供的`sleep`方法可以暂停当前执行的过程一段具体时间。如果当前线程被其他线程中断，sleep方法会抛出一个已检查异常——InterruptedException。

使用`Runnable`有一个缺陷就是无法返回执行的结果，`Callable`接口定义了带返回值的任务，


## ExecutorService、ThreadPool

为了资源的复用、提高响应速度以及方便的管理线程，`Executor`框架应运而生，该框架将线程的创建和管理与应用分离开来。

`Executor`框架的主要作用：
* 线程创建：使用线程池创建线程
* 线程管理：管理线程的生命周期，调用者不需要关心线程的状态
* 任务提交和执行：Executor框架提供了提交任务给线程池的方法，同时给予你决定任务何时被执行的权利，即你可以决定提交的任务立即被执行或者安排任务延迟或者定期执行。

Java`JCU`包中提供了三个`Executor`接口，提供了创建和管理线程的所有方面：
* `Executor`：包含了一个`execute`方法的接口，该方法通过接受一个`Runnable`对象来启动一个任务
* `ExecutorService`：是`Executor`的子接口，添加了一些功能去管理任务的生命周期。同时提供了`submit`方法，通过重载该方法接受`Runnable`和`Callable`的对象。
* `ScheduledExecutorService`：是`ExecutorService`的子接口，添加了定时执行任务的功能。

ExecutorService可以通过以下两个方法关闭：
* `shutDown`：当该方法被调用后，`ExecutorService`将停止接受新的任务，当目前执行的任务全部结束后，停止当前的`Executor`
* `shutDownNow`：当该方法被调用后，将会中断当前正在执行的任务并且立即关闭`Executor`

大多数的`Executor`实现都使用线程池来执行。线程池包含了一些执行任务的工作线程，这些线程被`Executor`所管理。

创建线程是一个昂贵开销的操作，应该尽量减少线程的创建。线程池通过重用线程来减少创建线程带来的开销。

任务被提交给线程池中的阻塞队列中，当提交的任务数超过活跃的线程时，新提交的任务将会被插入到阻塞队列中等待。线程池的详细参数和工作原理将会单独有一篇文章介绍。

## Future

我们可以提交`Callable`的任务给`ExecutorService`，但我们如何获取其执行的结果呢？`Future`对这种异步计算的情况进行了建模。

`subimit`方法提交的任务将会交给某个线程去执行，然而它不知道这个任务何时执行结束，返回值可用，因此它返回了一种特殊的类型——`Future`，该类型可以在结果可用时使用`future.get()`获取它，同时提供了`isDone()`方法来检测是否执行完成。

可以通过`future.cancel()`来取消执行任务，调用后将会尝试取消执行成功将会返回`true`否则为`false`。

`cancel`方法接受Boolean类型的参数——`mayInterruptIfRunning`。当传入`true`时，当前任务正在执行时将会被中断，否则当前任务允许执行完毕。

可以使用`isCancelled`方法检查任务是否被取消，当然取消之后的任务 `isDone()`将永远返回`true`

`get()`方法还提供了带超时时间的调用方式，避免在执行过程中因为某些服务不可用导致`get`永远阻塞。

通过调用`invokeAll()`方法可以批量执行`Callable`任务，将会返回`Future`组成的`List`。

`invokeAny()`方法将会返回执行最快任务的结果。


## CompletableFuture

`CompletableFuture`是`Future`的扩展，用来实现并发，充分利用CPU，避免因为等待远程服务返回而阻塞线程的执行。

`CompletableFuture`提供了比`Future`更多更方便的特性：
* 异常管理机制
* 使用`thenCompose`可以将两个异步计算合成为一个，这两个异步计算之间相互独立，同时第二个依赖第一个的结果
* 使用`thenCombine`可以将两个异步计算进行合并，两个异步计算之间相互独立且无依赖
* 等待`Future`中的所以任务都完成
* 仅等待`Future`执行最快的任务完成，并返回结果
* 通过编程的方式完成一个`Future`任务的执行
* 应对Future的完成事件，能够使用`Future`计算的结果进行下一步操作
* 可以使用定制的执行器，调整线程数量使之符合场景


## 参考资料
<https://www.callicoder.com/java-callable-and-future-tutorial/>
<https://juejin.im/post/5adbf8226fb9a07aac240a67>
