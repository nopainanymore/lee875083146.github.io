---
title: Java8如何使用Stream
entitle: Java8-Stream-usage
date: 2019-03-17 11:57:53
tags: [Java,Java8,Stream]
categories:
- Java
- Java8
- Stream
---

介绍常用的StreamAPI。
本文中的例子GitHub地址：<https://github.com/Lee875083146/java8/blob/master/src/main/java/com/nopainanymore/java8/Stream/StreamOperation.java>

<!--more-->

例子中使用的类：

```java
@Data
public class Student {

    private String name;

    private Integer age;

    private String sex;

    private Integer stuId;

    private Integer classId;

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

```

# collect()
函数原型为`<R, A> R collect(Collector<? super T, A, R> collector);`，使用`Collector`作为参数，一般使用`Collectors`类实现的各种收集操作，例如`toList`，`toSet`等。`Collectors`类中还实现了一些有意思的静态方法例如`groupingBy`,`partitioningBy`等。

# filter()
函数原型为`Stream<T> filter(Predicate<? super T> predicate);`，其作用是返回满足`predicate`元素的Stream。
例子：

```java
    private static void filter(List<Student> studentList) {
        log.info("StreamOperation- filter- before :{}", gson.toJson(studentList));
        List<Student> afterFilter = studentList
                .stream()
                .filter(student -> MALE.equals(student.getSex()))
                .collect(Collectors.toList());
        log.info("StreamOperation- filter- after:{}", gson.toJson(afterFilter));
    }
```

执行结果：

```
23:55:33.560 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- filter- before :[{"name":"a1","age":21,"sex":"女","stuId":1401},{"name":"a2","age":20,"sex":"男","stuId":1402},{"name":"a3","age":21,"sex":"女","stuId":1403},{"name":"a4","age":20,"sex":"男","stuId":1404}]
23:55:33.563 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- filter- after:[{"name":"a2","age":20,"sex":"男","stuId":1402},{"name":"a4","age":20,"sex":"男","stuId":1404}]
```
使用filter达到了将性别为女的学生过滤。

# map()
函数原型为`<R> Stream<R> map(Function<? super T, ? extends R> mapper);`，其作用返回当前所有元素执行mapper之后的结果组成的Stream。
例子：

```java
    private static void map(List<Student> studentList) {
        log.info("StreamOperation- map- before:{}", gson.toJson(studentList));
        List<String> nameString = studentList
                .stream()
                .map(Student::getName)
                .collect(Collectors.toList());
        log.info("StreamOperation- map- after:{}", gson.toJson(nameString));
    }
```

执行结果：

```
12:15:24.636 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- map- before:[{"name":"a1","age":21,"sex":"女","stuId":1401},{"name":"a2","age":20,"sex":"男","stuId":1402},{"name":"a3","age":21,"sex":"女","stuId":1403},{"name":"a4","age":20,"sex":"男","stuId":1404}]
12:15:24.641 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- map- after:["a1","a2","a3","a4"]
```

通过map获取到了所有学生的姓名


# mapToInt(), mapToDouble(), mapToLong()
三者的函数原型分别为`IntStream mapToInt(ToIntFunction<? super T> mapper);`，` DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper);`，`LongStream mapToLong(ToLongFunction<? super T> mapper);`，分别返回`IntStream`、`DoubleStream`、`LongStream`三种基本类型流，基本类型流可以使用特殊的lambda表达式，同时支持一些聚合方法，如：`sum()`，`average()`等。
例子：

```java
    private static void mapToInt(List<Student> studentList) {
        log.info("StreamOperation- mapToInt- before:{}", gson.toJson(studentList));
        Double avgAge = studentList
                .stream()
                .mapToInt(Student::getAge)
                .average()
                .orElse(0);
        log.info("StreamOperation- mapToInt- result:{}", avgAge);
    }
```
执行结果：

```
12:25:25.829 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- mapToInt- before:[{"name":"a1","age":21,"sex":"女","stuId":1401},{"name":"a2","age":20,"sex":"男","stuId":1402},{"name":"a3","age":21,"sex":"女","stuId":1403},{"name":"a4","age":20,"sex":"男","stuId":1404}]
12:25:25.833 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- mapToInt- result:20.5
```
使用`mapToInte()`将`Student`流转化为年龄的`IntStream`，使用`average()` 获取`OptionalDouble`类型的结果，求出了学生的平均年龄.

# flatMap()
函数原型为`<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);`，`flatMap`可以将一个stream中的每一个元素对象转换为另一个stream中的另一种元素对象，因此可以将stream中的每个对象改造成零，一个或多个。flatMap操作的返回流包含这些改造后的对象。

例子：

```java
    private static void flatMap(List<List<Student>> classList) {
        log.info("StreamOperation- flatMap- before:{}", gson.toJson(classList));
        List<Student> students = classList
                .stream()
                .flatMap(Collection::stream)
                .collect(Collectors.toList());
        log.info("StreamOperation- flatMap- after:{}", gson.toJson(students));
    }

```

执行结果：

```
12:49:49.925 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- flatMap- before:[[{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":1},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":1},{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":1}],[{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":2},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":2},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":2},{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":2}]]
12:49:49.934 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- flatMap- after:[{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":1},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":1},{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":1},{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":2},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":2},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":2},{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":2}]
```

通过`flatMap`将两个`List<Student>`组成的`classList`转化为一个`List<Student>`。


# peek()
函数原型`Stream<T> peek(Consumer<? super T> action);`，返回包含流中所有元素的流并为流中所以元素执行所提供的操作，该方法存在的目的是debug，可以用此方法

例子：

```java
    private static void peek(List<Student> studentList) {
        log.info("StreamOperation- peek- before:{}", gson.toJson(studentList));
        Long numOfMale = studentList
                .stream()
                .peek(student -> log.info("first peek Student:{}", gson.toJson(student)))
                .filter(student -> MALE.equals(student.getSex()))
                .peek(student -> log.info("second peek Student:{}", gson.toJson(student)))
                .count();
        log.info("StreamOperation- peek- numOfMale:{}", numOfMale);
    }
```
结果：

```
19:45:58.814 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- peek- before:[{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":1},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":1},{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":1}]
19:45:58.820 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - first peek Student:{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":1}
19:45:58.820 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - first peek Student:{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1}
19:45:58.820 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - second peek Student:{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1}
19:45:58.820 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - first peek Student:{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":1}
19:45:58.820 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - first peek Student:{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":1}
19:45:58.820 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - second peek Student:{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":1}
19:45:58.820 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- peek- numOfMale:2
```
由日志打印出的信息我们也可以看到流构成的函数管道，将所有中间操作全部融合后执行。
例子中也使用了`count()`来统计性别是男的学生人数。

# distinct()
函数原型`Stream<T> distinct();`，返回由该流中`equals`决定的唯一的元素组成的流，即用来去重。

例子：

```java
    private static void distinct(List<Student> studentList) {
        log.info("StreamOperation- distinct- before :{}", gson.toJson(studentList));
        List<Integer> classIdList = studentList
                .stream()
                .map(Student::getClassId)
                .distinct()
                .collect(Collectors.toList());
        log.info("StreamOperation- distinct- classIdList :{}", gson.toJson(classIdList));
    }
```
执行结果：
```
20:14:59.964 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- distinct- before :[{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":1},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":1},{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":1},{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":2},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":2},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":2},{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":2}]
20:14:59.974 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- distinct- classIdList :[1,2]
```
通过`map`方法获取`classId`的集合，通过`distinct`去重获得不重复的班级。


# sorted(comparator)
函数原型`Stream<T> sorted(Comparator<? super T> comparator);`，返回一个由`comparator`决定的排序之后的流。

例子：

```java
    private static void sorted(List<Student> studentList) {
        log.info("StreamOperation- sorted- before:{}", gson.toJson(studentList));
        List<Student> sorted = studentList
                .stream()
                .sorted((o1, o2) -> o2.getStuId() - o1.getStuId())
                .collect(Collectors.toList());
        log.info("StreamOperation- sorted- :{}", gson.toJson(sorted));
    }

```

执行结果：

```
20:38:52.395 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- sorted- before:[{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":1},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":1},{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":1}]
20:38:52.405 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- sorted- :[{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":1},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":1},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1},{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":1}]
```
传入比较学号的lambda表达式，为了逆序用后一个学号减去前一个学号。

# limit()
函数原型`Stream<T> limit(long maxSize);`，截取流中前`maxSize`组成的元素组成新的流。

```java
    private static void limit(List<Student> studentList) {
        log.info("StreamOperation- limit- before:{}", gson.toJson(studentList));
        List<Student> limit = studentList
                .stream()
                .limit(2)
                .collect(Collectors.toList());
        log.info("StreamOperation- limit- after:{}", gson.toJson(limit));
    }
```

执行结果：

```
20:40:22.430 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- limit- before:[{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":1},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1},{"name":"a3","age":21,"sex":"女","stuId":1403,"classId":1},{"name":"a4","age":20,"sex":"男","stuId":1404,"classId":1}]
20:40:22.433 [main] INFO com.nopainanymore.java8.Stream.StreamOperation - StreamOperation- limit- after:[{"name":"a1","age":21,"sex":"女","stuId":1401,"classId":1},{"name":"a2","age":20,"sex":"男","stuId":1402,"classId":1}]
```
截取了前两个学生。

# 其他API
Stream中还有很多API在用到时，直接看源码及源码注释即可。



# 参考资料
[Java 8 中的 Streams API 详解](https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/)
[Java 8 Stream 教程](https://www.jianshu.com/p/0c07597d8311)
[函数纯度](https://www.ibm.com/developerworks/cn/java/j-java8idioms11/index.html)
[Java 8 Stream探秘](https://colobu.com/2014/11/18/Java-8-Stream/)
[JavaLambdaInternals](https://github.com/CarpenterLee/JavaLambdaInternals)