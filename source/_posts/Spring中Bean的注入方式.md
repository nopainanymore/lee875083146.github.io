---
title: Spring中Bean的注入方式
tags: [Spring,DI,依赖注入]
categories:
- Spring
date: 2020-01-09 20:00:15
entitle: Spring-Injection
---

本文探讨Spring团队为什么推荐构造方法注入。

<!--more-->

之前一直都是用构造方法注入，现在的项目中很多都是成员变量注入，Spring的依赖注入有三种形式，当使用成员变量注入时，IDEA将会有以下提示，为什么Spring团队推荐使用构造方法注入呢？本文将探讨一下这个问题。

[!Spring-Team—Recommend](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/Spring/%E6%88%AA%E5%B1%8F2020-01-2011.01.24.png?x-oss-process=style/sw-white)

## 注入方式

* 构造方法注入
* 成员变量注入
* setter注入

现在流行的一般是构造方法注入和成员变量两种，那么为什么Spring团队推荐使用构造方法注入呢？在StackOverFlow以及[Oliver Gierke](https://spring.io/team/ogierke)的一篇[博客](http://olivergierke.de/2013/11/why-field-injection-is-evil/)上找到了相关的讨论和分析。

> Oliver Gierke is the lead of the Spring Data project at Pivotal, formerly known as SpringSource, and member of the JPA 2.1 expert group.


## StackOverflow上的讨论

StackOverFlow的问题见<https://stackoverflow.com/questions/40620000/spring-autowire-on-properties-vs-constructor>，我选择了我认为解答比较好的两个回答做了翻译。

### 问题

Spring中Service的依赖有以下两种写法，我知道两种方式都可以工作，但是使用第二种有什么优势呢？它在写类和单测时需要写更多的代码。我是否有遗漏的地方，这种方式是一种更好的依赖注入方式吗？

```java
@Component
public class SomeService {
     @Autowired private SomeOtherService someOtherService;
}
```

I have now run across code that uses another convention to achieve the same goal

```java
@Component
public class SomeService {
    private final SomeOtherService someOtherService;

    @Autowired
    public SomeService(SomeOtherService someOtherService){
        this.someOtherService = someOtherService;
    }
}
```

### 回答及讨论

#### 回答一

是的，比起成员变量注入确实更加推荐第二种方式（构造方法注入）。它有以下几个优点：

* 使用构造方法注入时，依赖是十分明确的。当在测试或者在其他任意场景实例化一个对象的时候，是不会忘记任何依赖的。
* 依赖被`final`修饰，这对代码的健壮性和线程安全起到帮助作用
* 不需要使用反射来设置依赖。使用`@InjectMocks`时仍然起作用但是不是必要的。你可以创建自己mock的对象，并通过简单的调用构造方法将他们注入。

—— JB Nizet

讨论:

让我们深入挖掘，假设你有一些其他的配置，例如`@Value("some.prop") private String property`，你也会将它一起设置到构造方法里面吗？这最终好像只会让你得到一个拥有非常长的参数的构造方法。它本身不坏只不过是有更多的代码。
—— GSUgambit

是的，你应该将这些配置也放到构造方法中。当构造方法的参数过多是，常常是你应该将类分解成更多小的类的标志，让每个类拥有更少的自己的职责和依赖。
—— JB Nizet


#### 回答二

在方式一中，允许任意的类（无论是否在`Spring`容器中）都可以使用默认的构造方法去创建实例（`new SomeService()`），当你在你需要一个`SomeOtherService`作为`SomeService`的依赖时，这样做是不好的。

那么除了将代码添加到单元测试中，构造方法注入还有其他功能吗？这是进行依赖注入更好的方法吗？

方式二是首选的方法，因为在实际没有解决`SomeOtherService`依赖的时候，他不允许去创建一个`SomeService`。

—— developer


## Oliver Gierke博文

原文链接：<http://olivergierke.de/2013/11/why-field-injection-is-evil/>

讨论这个问题的上下文是：我们希望去实现一个`Component`持有一个`Collaborator`。众所周知，依赖注入是链接两个`Component`最直接简单的方法，例如：

```java
class MyComponent {

  @Inject MyCollaborator collaborator;

  public void myBusinessMethod() {
    collaborator.doSomething();
  }
}
```

### NPE

```java
  MyComponent component = new MyComponent();
  component.myBusinessMethod(); // -> NullPointerException
```

这个问题的核心在于，你的代码是否允许客户端去创建一个处于无效状态的实例。类存在的目的就是客户端能够强制依赖一个不变量。这也是你代码中使用`EmailAddress`类而不是简单使用一个`String`去表示email address的原因之一。由于在构造方法能保证强制约束类对象的合法性，所以就能保证客户端获取的`EmailAddress`实例是有效的，而`String`则无法保证，它可以任何东西，无论是否有效。

所以你可以猜想出这个将指向构造方法注入，让我们用体现我刚刚概述特征的方式去重构上述的代码：

```java
class MyComponent {

  private final MyCollaborator collaborator;

  @Inject
  public MyComponent(MyCollaborator collaborator) {
    Assert.notNull(collaborator, "MyCollaborator must not be null!");
    this.collaborator = collaborator;
  }

  public void myBusinessMethod() {
    collaborator.doSomething();
  }
}
```

我们可以概括出来：
1. 只能通过提供一个`MyCollaborator`来创建一个`MyComponent`的实例。强制客户端提供一个必须存在的依赖，确保所有的对象通过构造方法创建之后是有效的。
2. 将依赖通过构造方法的方式声明成为强制性的。成员变量注入只不过是想把丑陋的事物做成美好事物而做的无用功，只看公共接口你依然不知道依赖关系。特别是如果你在项目间分享代码，那么成员变量注入将会使方法变成重复循环“执行-等待NPE发生-声明缺失的`Bean`”的方法。
3. `final`类型的字段也增加了应用程序组件的稳定性。可以通过是否被final修饰可以清楚的分辨哪些是强制依赖哪些是可选依赖。

还有一个经常遇到的争论是构造方法中的变量太多了。一个类依赖的增长将导致不好的结果，这标志着你应该思考是否应该将一个`Component`分解成多个。

### 可测试性

回到对构造方法注入方式代码量对讨论上。假设我们使用成员变量对注入方式，我们的代码量确实会少一些，但是我猜你也在为你的类写测试代码，所以，在测试中你是如何将一个依赖注入到你的`Component`中呢？

```java
  MyCollaborator collaborator = … // mock dependency
  MyComponent component = new MyComponent();
  // Inject dependency by some reflection magic
  component.myBusinessMethod();
```

反射是一个解决办法，但是无论你是用一些帮助工具或者类似的方法去是你写起来舒服，这种解决方式仍然是一个比较乱的方法，不是吗？特别是当替代的方法比较简单的时候：

```java
  MyCollaborator collaborator = … // mock dependency
  MyComponent component = new MyComponent(collaborator);
  component.myBusinessMethod();
```

当你为被测试的类增加依赖、重构代码或者没有设置依赖的各种情况下，通过构造方法调用的时候都可以使代码完成。

### 样板打破者——Lombok

诚然，我在开始使用构造方法注入的时候，被要写的代码量所打击。这显然是Java的语言缺陷。不幸的是，很多面向对象的好的实践，比如ValueObjects，委派大于继承，构造方法注入等在Scala这样的语言中是很容易实现的。

然而，ProjectLombok在减少很多样板代码方面起了很大的作用。在Lombok中有很多有用的的特性，但是我想集中在与本次讨论相关的地方。使用Lombok时，我的Component的构造方法注入代码像这样：

```java
@RequiredArgsConstructor(onConstructor = @__(@Inject))
class MyComponent {

  private final @NonNull MyCollaborator collaborator;

  public void myBusinessMethod() {
    collaborator.doSomething();
  }
}
```

@RequiredArgsConstructor 注解在编译阶段将生成一个包含所有final字段的构造方法，额外的@NonNull注解将会检查参数是否为null。这个奇怪的 onConstructor是Lombok让你在生成的构造方法上添加注解的方式，所以通过额外的注解你可以有效的获取你想要的API。

### 对比

注入方式|代码量|安全性|测试难度
-|-|-|-
成员变量注入|少|不安全|测试较复杂
构造方法注入|多|安全|容易测试


## 总结

Spring团队之所以推荐使用构造方法注入主要是以下几点原因：
* 通过构造方法，声明依赖，避免了在测试或者其他环境下依赖的缺失
* 避免了因依赖确实而导致的NPE
* 增强了程序的稳定性，可测试性


## 参考资料
<https://stackoverflow.com/questions/40620000/spring-autowire-on-properties-vs-constructor>
<http://olivergierke.de/2013/11/why-field-injection-is-evil/>
