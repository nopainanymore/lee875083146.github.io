---
title: SpringBoot Bean的后置操作
tags: [SpringBoot,Bean]
categories:
- SpringBoot
- Bean
date: 2019-10-21 16:39:51
entitle: SpringBoot-Bean-PostOperation
---

本文介绍SpringBoot中Bean的后置操作的实现方式及他们之间的执行顺序。

<!--more-->

在SpringBoot应用中，我们常常会需要在自定义Bean初始化后做一些额外的定制操作，常见的一般有以下四种：@PostConstruct、InitializingBean、ApplicationRunner、CommandLineRunner，下面将介绍四种实现方式及他们之间的区别。


## @PostConstruct

> The PostConstruct annotation is used on a method that needs to be executed after dependency injection is done to perform any initialization. This method MUST be invoked before the class is put into service. This annotation MUST be supported on all classes that support dependency injection. The method annotated with PostConstruct MUST be invoked even if the class does not request any resources to be injected. Only one method can be annotated with this annotation. The method on which the PostConstruct annotation is applied MUST fulfill all of the following criteria:
> * The method MUST NOT have any parameters except in the case of interceptors in which case it takes an InvocationContext object as defined by the Interceptors specification.
> * The method defined on an interceptor class MUST HAVE one of the following signatures:
> void <METHOD>(InvocationContext)
>
> Object <METHOD>(InvocationContext) throws Exception
>
> Note: A PostConstruct interceptor method must not throw application exceptions, but it may be declared to throw checked exceptions including the java.lang.Exception if the same interceptor method interposes on business or timeout methods in addition to lifecycle events. If a PostConstruct interceptor method returns a value, it is ignored by the  container.
>
> * The method defined on a non-interceptor class MUST HAVE the following signature:
> void <METHOD>()
>
> * The method on which PostConstruct is applied MAY be public, protected, package private or private.
> * The method MUST NOT be static except for the application client.
> * The method MAY be final.
> * If the method throws an unchecked exception the class MUST NOT be put into service except in the case of EJBs where the EJB can handle exceptions and even recover from them.

`@PostConstruct`被使用在当依赖注入已经完成后需要执行当方法上。该方法必须在使用之前被调用。所有支持依赖注入当类都必须支持该注解。即使这个类没有任何需要注入的资源，这个方法也必须被调用。一个类中只能有一个方法被该注解注释。且该方法必须满足以下条件：
* 除了在拦截器情况下，这个方法不能有任何参数。拦截器情况下，该方法使用被Interceptors规范定义的InvocationContext对象。
* 定义在拦截器的该方法必须是以下签名的其中之一，`void <METHOD>(InvocationContext)`，`Object <METHOD>(InvocationContext) throws Exception`
* 定义在肥拦截器的类上时，必须是`void <METHOD>()`
* 该方法可以是 `public`, `protected`, `package private` 或者 `private`
* 该方法不能为静态方法
* 该方法可以是`final`的
* 如果该方法抛出了未检查的异常，除非EJB可以处理异常或者从异常恢复，否则该类不能被使用。

## InitializingBean

接口注释：

> Interface to be implemented by beans that need to react once all their properties have been set by a BeanFactory: e.g. to perform custom initialization, or merely to check that all mandatory properties have been set.

当一个Bean需要在`BeanFactory`设置完所有属性后将调用某个方法（`void afterPropertiesSet() throws Exception;`）可以实现该接口。该方法可以用来执行自定义的初始化操作，或者检查所有必须的属性都已经被设置。

方法注释：

> Invoked by the containing BeanFactory after it has set all bean properties and satisfied BeanFactoryAware, ApplicationContextAware etc. This method allows the bean instance to perform validation of its overall configuration and final initialization when all bean properties have been set.

`afterPropertiesSet()`方法在所有属性都被设置并满足`BeanFactoryAware`， `ApplicationContextAware`等之后，由`BeanFactory`调用执行。该方法允许Bean实例执行所有配置的验证和所有属性设置之后的最终初始化操作。

## ApplicationRunner

> Interface used to indicate that a bean should run when it is contained within a SpringApplication. Multiple ApplicationRunner beans can be defined within the same application context and can be ordered using the Ordered interface or @Order annotation.

该接口用来指明当一个Bean被包含在`SpringApplication`时应该执行，多个`ApplicationRunner`可以被定义在同一个应用上下文中，并且可以通过实现`Ordered`接口或者使用`@Order`注解为其排序。

## CommandLineRunner
> Interface used to indicate that a bean should run when it is contained within a SpringApplication. Multiple CommandLineRunner beans can be defined within the same application context and can be ordered using the Ordered interface or @Order annotation.
> If you need access to ApplicationArguments instead of the raw String array consider using ApplicationRunner.

该接口用来指明当一个Bean被包含在`SpringApplication`时应该执行，多个`CommandLineRunner`可以被定义在同一个应用上下文中，并且可以通过实现`Ordered`接口或者使用`@Order`注解为其排序。

如果需要获取`ApplicationArguments`而不是String数组，考虑使用`ApplicationRunner`。

## 执行顺序

如下所示，测试四种方式的执行顺序。

```java
@Component
@Slf4j
public class PostOperationComponent implements InitializingBean, ApplicationRunner, CommandLineRunner {

    @PostConstruct
    private void postConstruct() throws InterruptedException {
        log.info("PostOperationComponent- postConstruct- postConstruct");
        TimeUnit.SECONDS.sleep(1);
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        log.info("PostOperationComponent- afterPropertiesSet- initializingBean");
        TimeUnit.SECONDS.sleep(1);
    }

    @Override
    public void run(ApplicationArguments args) throws Exception {
        log.info("PostOperationComponent- run- ApplicationRunner");
        TimeUnit.SECONDS.sleep(1);
    }

    @Override
    public void run(String... args) throws Exception {
        log.info("PostOperationComponent- run- CommandLineRunner");
        TimeUnit.SECONDS.sleep(1);
    }
}
```

![PostOperationTime](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/DeepInSpring/PostOperationTime.png?x-oss-process=style/sw-white
)

由图中日志所示，四种方式的执行顺序为：`@PostConstruct` -> `InitializingBean` -> `ApplicationRunner` -> `CommandLineRunner`。
