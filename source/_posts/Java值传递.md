---
title: Java值传递
tags: [Java,值传递]
categories:
- Java
date: 2019-11-11 14:10:25
entitle: Java-PassByValue
---

最近看到<https://blog.biezhi.me/2018/11/java-pass-by-value.html>对Java中对值传递的分析解读，之前也在[hollis的一篇文章](https://www.hollischuang.com/archives/2275)中看到过关于Java值传递的分享。

Java中存在引用传递吗？Java中到底是什么传递。本文将详细解读。

<!--more-->

## 形参与实参
首先先了解形参与实参的概念。

* 形参：在定义函数名和函数体的时候使用的参数，目的是用来接收调用该函数时传入的参数。
* 实参：在调用有参函数时，主调函数和被调函数之间有数据传递关系，在主调函数中调用一个函数时，函数名后面括号中的参数即为实参

实参即是在调用有参函数的时候真正传递的内容，而形参是用于接收实参内容的参数。

## 值传递与引用传递

* 值传递：在调用函数时，将实际参数**拷贝**一份传递到函数中，这样在函数中如果对参数进行调整，将不会影响到实际参数。
* 引用传递：在调用函数时将实际参数的地址直接传递到函数中，在函数中对参数所进行的修改，将影响到实际参数

> 它们都是在发生函数调用时的一种参数求值策略，这是对调用函数时，求值和传值的方式的描述，而非传递的内容类型（内容指：是值类型还是引用类型，是值还是指针）。值类型/引用类型，是用于区分两种内存分配方式，值类型在栈上分配，引用类型在堆上分配。一个描述内存分配方式，一个描述参数求值策略，两者之间无任何依赖或约束关系。

所以两种参数求值策略的根本区别就在于**是否会对传入的实际参数进行拷贝**。如果对这两个基本概念没有了解，那么对参数传递的讨论也就没有意义，这也是有些人对Java参数传递产生的误解的原因。

## Java的值传递

接下来对Java中的值传递进行分析。

Java采取的是值传递的参数求值策略，即在进行传参时，被调函数会复制一份传入的值，对这个值进行操作。

### 入参为基本类型

当入参为基本类型时，被调函数将会将基本类型进行拷贝，得到一个与入参值相等的新的基本类型值，但这两个值所在的内存地址是不同的，所以在被调函数中所有对新值的操作都不会反馈在入参值上。

例如：

```java
private static void change(int num) {
    log.info("'PassByValue'- change- num:{}", num);
    num = 1;
    log.info("PassByValue- change- num:{}", num);
}

public static void main(String[] args) {
    int num = 233;
    log.info("PassByValue- main- before num:{}", num);
    change(num);
    log.info("PassByValue- main- after num:{}", num);
}
```

输出为：
```java
12:39:52.719 [main] INFO com.nopainanymore.java8.practice.PassByValue - PassByValue- main- before num:233
12:39:52.722 [main] INFO com.nopainanymore.java8.practice.PassByValue - PassByValue- change- num:233
12:39:52.722 [main] INFO com.nopainanymore.java8.practice.PassByValue - PassByValue- change- num:1
12:39:52.722 [main] INFO com.nopainanymore.java8.practice.PassByValue - PassByValue- main- after num:233
```


### 入参为对象

当入参为对象类型，同样被调函数将会拷贝一份入参，在这种情况下拷贝的将会是**入参对象的内存地址**，在被调函数中对这个对象操作时，因为入参拷贝的是内存地址，所以将会对原始的数据进行操作，看起来就像是传递了引用一样。

例如：

```java
@Data
@AllArgsConstructor
static class Value {
    Integer num;
}

private static void change(Value value) {
    log.info("PassByValue- change- value:{}, identityHashCode:{}", gson.toJson(value), System.identityHashCode(value));
    value.num = 233;
    log.info("PassByValue- change- value:{}, identityHashCode:{}", gson.toJson(value), System.identityHashCode(value));
}

public static void main(String[] args) {
    Value origin = new Value(1);
    log.info("PassByValue- main- before value:{} identityHashCode:{}", gson.toJson(origin), System.identityHashCode(origin));
    change(origin);
    log.info("PassByValue- main- after value:{} identityHashCode:{}", gson.toJson(origin), System.identityHashCode(origin));
}
```

输出为：

```java
13:03:08.981 [main] INFO com.nopainanymore.java8.practice.PassByValue - PassByValue- main- before value:{"num":1} identityHashCode:314337396
13:03:08.981 [main] INFO com.nopainanymore.java8.practice.PassByValue - PassByValue- change- value:{"num":1}, identityHashCode:314337396
13:03:08.981 [main] INFO com.nopainanymore.java8.practice.PassByValue - PassByValue- change- value:{"num":233}, identityHashCode:314337396
13:03:08.981 [main] INFO com.nopainanymore.java8.practice.PassByValue - PassByValue- main- after value:{"num":233} identityHashCode:314337396
```

从上述的例子来看，`value`对象的`identityHashCode`值从始至终都没有改变，即不管是主调函数还是被调函数中`value`对象都是同一个对象的内存地址。

结合一张图来看上述的操作。

![PassByValue](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/java/PassByValue.png?x-oss-process=style/sw-white)

`origin`与`value`都为对象在堆的内存地址，即都指向了同一个对象。

## 扩展——基本类型与对象内存地址的分配

### 方法中

在方法中声明的变量，即该变量是局部变量，每当程序调用方法时，系统都会为该方法建立一个方法栈，其所在方法中声明的变量就放在方法栈中，当方法结束系统会释放方法栈，其对应在该方法中声明的变量随着栈的销毁而结束，这就局部变量只能在方法中有效的原因。

在方法中声明的变量可以是基本类型的变量，也可以是引用类型的变量。

* 当声明是基本类型的变量的时，其变量名及值（变量名及值是两个概念）是放在方法栈中。
* 当声明的是引用变量时，所声明的变量（该变量实际上是在方法中存储的是内存地址值）是放在方法的栈中，该变量所指向的对象是放在堆类存中的。

### 类中

在类中声明的变量是成员变量，也叫全局变量，放在堆中的（因为全局变量不会随着某个方法执行结束而销毁）。

同样在类中声明的变量即可是基本类型的变量 也可是引用类型的变量

* 当声明的是基本类型的变量其变量名及其值放在堆内存中的。
* 引用类型时，其声明的变量仍然会存储一个内存地址值，该内存地址值指向所引用的对象。引用变量名和对应的对象仍然存储在相应的堆中。


> 按共享传递，是指在调用函数时，传递给函数的是实参的地址的拷贝（如果实参在栈中，则直接拷贝该值）。在函数内部对参数进行操作时，需要先拷贝的地址寻找到具体的值，再进行操作。如果该值在栈中，那么因为是直接拷贝的值，所以函数内部对参数进行操作不会对外部变量产生影响。如果原来拷贝的是原值在堆中的地址，那么需要先根据该地址找到堆中对应的位置，再进行操作。因为传递的是地址的拷贝所以函数内对值的操作对外部变量是可见的。


## 参考资料
<https://www.hollischuang.com/archives/2275>
<https://blog.biezhi.me/2018/11/java-pass-by-value.html>
<https://www.cnblogs.com/panxuejun/p/5970739.html>
