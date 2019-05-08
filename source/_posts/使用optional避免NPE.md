---
title: 使用Optional避免NPE
entitle: Java8-Optional
tags: [Java,Java8,Optional]
date: 2019-03-29 16:30:03
categories:
- Java
- Java8
- Optional
---

在Java开发过程中，null引发的NullPointException是最常见的问题，本文介绍了Java中的null及如何使用optional正确处理null，避免NPE。
<!--more-->


## Java中的null
对于Java程序员来说，NullPointException(NPE)是最常出现的异常，null设计的初衷是对“不存在的值”进行建模。

### null的一些特点
1. null是Java中的关键字。
2. null是所有引用类型的默认值。
3. 含有null值的包装类在Java拆箱生成基本数据时都会产生**NPE**。

### null带来的问题
1. 如果处理不正确将引起NPE，Java程序开发中最典型的异常。
2. 多层null检查，使代码膨胀，降低代码的可读性。
3. null本身没有任何语义，它代表的是在静态类型语言中以一种错误的方式对缺失变量的建模。
4. 破坏了Java的设计哲学，Java一直避免让程序员意式到指针的存在，唯一例外就是null。
5. null可以被赋值给任意一个引用类型的变量，当某个被赋nul的引用对象从系统的一个部分传递到另一个部分后，将无法获知此变量最初赋值的类型。


## Optional类
Java8中引入了`java.util.Optional`类处理null，Optional类是一个容器对象，包含了一个可能为null也可能不为null的对象。

### 介绍
![Optional Diagrams](http://i2.bvimg.com/686156/e77d5e1aaf207766.png "Optional Diagrams")

方法|描述|作用
:-|:-|-
`empty()`|返回一个空的Optional实例|用来创建一个value为null的Optional对象
`of(T value)`|返回必须含有非null值Optional，如果value为null则抛出NPE|用来创建一个optional对象，value必须为非null。
`ofNullable(T value)`|当value为null返回`empty()`，否则返回含有value的Optional|用来创建一个Optional对象，value可以为非null也可以为null
`get()`|当value为null时抛出NPE，否则返回类型为T的value值|用来获取Optional对象中的value，但强烈不推荐此用法，面将解释原因
`isPresent()`|返回`value != null`|用来判断Optional中的值是否为null
`ifPresent(Consumer<? super T> consumer)`|当`value != null`，执行`consumer`的`accept()`方法|当Optional值不为null是执行需要的lambda表达式
`filter(Predicate<? super T> predicate)`|当value不为null且满足`predicate.test()`方法时，返回value，否则返回`empty()`。当predicate为null时抛出NPE|用于判断Optional中的值是否满足条件，作用相当于if
`map(Function<? super T, ? extends U> mapper)`|当value不为null，对其执行提供的mappin函数调用，且结果不为null时将结果返回，否则返回`empty()`。当mapping方法为null时，抛出NPE|用于从Optional中提取和转换值
`flatMap(Function<? super T, Optional<U>> mapper)`|当value不为null，对其执行提供的mapping函数调用，返回一个Optional类型的值，否则返回`empty()`。当mapping方法为null时，抛出NPE|flatMap用于将多层的Optional对象合并为一个
`orElse(T other)`|当value不为null，返回value，否则返回other|当Optional中值为null时提供缺省的返回值
`orElseGet(Supplier<? extends T> other)`|当value不为null，返回value，否则调用`other.get()`方法，返回其执行结果|当Optional为null时返回一个接受supllier生成的缺省值
`orElseThrow(Supplier<? extends X> exceptionSupplier)`|当value不为null，返回value，否则抛出由supplier产生的异常|当Optional中的值为null时抛出指定的异常


## 使用Optional优雅地处理null
例子中用到的类：
```java
@Data
@Accessors(chain = true)
public class Student {

    public final static String MALE = "男";

    public final static String FEMALE = "女";

    private String name;

    private Integer age;

    private String sex;

    private Integer stuId;

    private Integer classId;

    private Pet pet;

    public Student() {
    }

    public Student(String name, Integer age, String sex, Integer stuId, Integer classId) {
        this.name = name;
        this.age = age;
        this.sex = sex;
        this.stuId = stuId;
        this.classId = classId;
    }

}

@Data
@Accessors(chain = true)
public class Pet {

    private String name;


}
```
### 错误使用方式
很多人在使用Optional时，先使用`isPresent()`判断Optional中是否为null，然后调用`get()`方法获取，但这样和原先`== null`没有任何区别，并不是Java8中Optional的正确使用姿势。
错误使用方式：
```java
Optional<Student> studentOpt = Optional.ofNullable(student);
        if (studentOpt.isPresent()) {
            student = studentOpt.get();
        } else {
            System.out.println("student is null");
        }
```
### 正确使用姿势
#### orElse、orElseGet、orElseThrow
使用这三个方法去获取Optional中的值。

```java
        Student student = studentOptional.orElse(new Student());
```

```java
        Student student1 = studentOpt.orElseGet(Student::new);
```

```java
      Student student = studentOpt.orElseThrow(() -> new RuntimeException("student is not found"));
```
#### map
`map()`用来获取或者转换对象中的属性。

```java
        String name = studentOpt
                .map(Student::getName)
                .map(String::toUpperCase)
                .orElse("no name");
```
#### flatmap
和`map`类似但用来处理获取的值是一个Optional的情况。
例子中使用`Optional.ofNullable(s.getPet())`来构造这种场景。
```java
        String petName = studentOpt
                .flatMap(s -> Optional.ofNullable(s.getPet()))
                .map(Pet::getName)
                .orElse("no name");
```

#### filter
filter可以对Optional中对象增加条件判断，进一步简化if代码。

```java
        Student student = studentOpt
                .filter(s -> s.getAge() > 10)
                .orElseThrow(() -> new RuntimeException("student age not match"));
```

#### 使用Optional的注意点
* Optional不应该是实体类的属性，Optional没有实现 `Serializable` 接口，不能被序列化
* 不要用Optional封装`List`，例如在查询对象列表时，当没有任何一个对象时返回`new ArrayList()`





