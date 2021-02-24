---
title: AOP与Spring-AOP
entitle: Spring-AOP
tags: [Spring,SpringBoot,AOP]
categories:
- Spring
- AOP
date: 2019-05-04 14:26:30
---

Spring AOP实现原理学习及如何在SpringBoot中使用。
[Github代码地址](https://github.com/Lee875083146/deep-in-spring)

<!--more-->

## AOP简介、用处

AOP(Aspect Orient Programming)，称为面向切面编程，作为面向对象的一种补充，用于处理系统中分布于各个模块的横切关注点，比如事物管理、日志、缓存等。

AOP实现的关键在于AOP框架自动创建的AOP代理，AOP代理主要分为静态代理和动态代理，静态代理表现为AspectJ，动态代理则以Spring AOP为代表。

AOP的出现解决外围业务代码与核心业务代码分离的问题，OOP的出现是把编码问题进行模块化，AOP就是把涉及到众多模块的某一类问题进行统一管理。功能代码不再单独入侵到核心业务类的代码中，即核心模块只需关注自己相关的业务。

## AOP中的概念

**Pointcut**：是一组基于正则表达式的表达式。通常一个pointcut会选择程序中的某些执行点，或执行点的集合。

**JoinPoint**：通过pointcut选取出来的集合中具体的一个执行点

**Advice**：在选取出来的JoinPoint上要执行的操作、逻辑，通知有以下五种类型。

* `before`目标方法执行前执行，前置通知
* `after`目标方法执行后执行，后置通知
* `after returning`目标方法返回时通知，后置返回通知
* `after throwing`目标方法抛出异常时执行，异常通知
* `around`在目标函数执行中执行，可控制目标函数是否执行(通过proceed)，环绕通知

**Aspect**：就是我们关注点的模块化。这个关注点可能会横切多个对象和模块，事务管理是横切关注点的很好的例子。是一个抽象的概念，从软件的角度来说是指在应用程序不同模块中的某一个领域或方面。由pointcut 和advice组成。

**Weaving**：把切面应用到目标对象并创建新的代理对象的过程。切面在指定的连接点被织入到目标对象。在目标对象的生命周期里有多个点可以进行织入：
* 编译期：切面在目标类编译时被织入。Aspect的织入编译器就是以这种方式织入切面的。
* 类加载期：切面在目标类加载到JVM时被织入。需要特殊的类加载(Classloader)，它可以在目标类被引入之前增强该目标类的字节码(CGlib)
* 运行期：切面在应用运行时的某个时刻被织入。AOP会为目标对象创建一个代理对象

## AspectJ

AspectJ是一个Java实现的AOP框架，它能够对Java代码进行AOP编译，让Java代码具有AspectJ的AOP功能。

```
pointcut 函数名 : 匹配表达式
```

### AspectJ织入方式

AspectJ采用静态织入的方式。主要采用的是编译期织入，在这个期间AspectJ的*acj*编译器(类似javac)把`aspect`类编译成class字节码后，在java目标类编译时织入，即先编译`aspect`类再编译目标类。除了编译期织入还存在链接期织入，即将`aspect`类和java目标类同时编译成字节码文件后，再进行织入处理，有助于已编译好的第三方jar和Class文件进行织入操作。

## Spring AOP

Spring AOP 与ApectJ 的目的一致，都是为了统一处理横切业务，但与AspectJ不同的是，Spring AOP 并不尝试提供完整的AOP功能，**Spring AOP 更注重的是与Spring IOC容器的结合**，并结合该优势来解决横切业务的问题，因此在AOP的功能完善方面，相对来说AspectJ具有更大的优势，同时，Spring采用动态代理技术的实现原理来构建Spring AOP的内部机制，这是与AspectJ最根本的区别。

### 动态代理

Spring AOP使用动态代理，动态代理不会去修改字节码，而是在内存中临时为方法生成一个AOP对象，该对象包含了对象目标的全部方法，并且在特定的切点做了增强处理，并调用原对象的方法。动态代理分为JDK动态代理和CGLIB动态代理。

### JDK动态代理

JDK动态代理基于反射技术实现。

```java
public interface ExInterface {

    void execute();
}

@Slf4j
public class EClass implements ExInterface {

    @Override
    public void execute() {
        log.info("EClass- execute- execute method");
    }
}

@Slf4j
public class JDKProxy implements InvocationHandler {

    private EClass target;

    public JDKProxy(EClass target) {
        this.target = target;
    }

    public ExInterface createProxy(){
        return (ExInterface) Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if (method.equals(ExInterface.class.getDeclaredMethod("execute"))) {
            log.info("JDKProxy- invoke- before");
            Object result = method.invoke(target, args);
            log.info("JDKProxy- invoke- after");
            return result;
        }
        return method.invoke(target, args);
    }


    public static void main(String[] args) {
        EClass eClass = new EClass();
        JDKProxy proxy = new JDKProxy(eClass);
        proxy.createProxy().execute();
    }
}

```

被代理的类必须实现接口，JDK提供的Proxy类将通过目标对象的类加载器和接口，以及句柄创建与目标对象类具有相同接口的代理对象proxy，代理对象的创建是通过Proxy类达到的，Proxy类由Java JDK提供，利用Proxy#newProxyInstance方法便可以动态生成代理对象(proxy)，底层通过反射实现的。该代理对象将拥有目标对象所实现接口中的所有方法，同时代理类必须实现InvokeHandler接口并重写invoke方法，**当调用proxy的每个方法时，invoke方法将被调用**，利用该特性可以在invoke方法中对目标对象方法执行的前后进行添加其他的操作，无需触及目标对象的任何代码，实现了AOP功能。缺点在于需要有接口。

>当代理对象生成后，最后由InvocationHandler的invoke()方法调用目标方法:

在动态代理中InvocationHandler是核心，每个代理实例都具有一个关联的调用处理程序(InvocationHandler)。
对代理实例调用方法时，将对方法调用进行编码并将其指派到它的调用处理程序(InvocationHandler)的invoke()方法。
所以对代理方法的调用都是通InvocationHadler的invoke来实现中，而invoke方法根据传入的代理对象，方法和参数来决定调用代理的哪个方法。

### CGLIB动态代理

CGLIB动态代理基于继承的方式实现，免去了接口的实现。

```java
@Slf4j
public class EClass {

    public void execute() {
        log.info("EClass- execute- execute");
    }
}

@Slf4j
public class CGLibProxy implements MethodInterceptor {

    private EClass target;

    public CGLibProxy(EClass target) {
        this.target = target;
    }

    public EClass createProxy() {
        // 使用CGLIB生成代理:
        // 1.声明增强类实例,用于生产代理类
        Enhancer enhancer = new Enhancer();
        // 2.设置被代理类字节码，CGLIB根据字节码生成被代理类的子类
        enhancer.setSuperclass(target.getClass());
        // 3.//设置回调函数，即一个方法拦截
        enhancer.setCallback(this);
        // 4.创建代理:
        return (EClass) enhancer.create();
    }

    @Override
    public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        if (method.equals(EClass.class.getDeclaredMethod("execute"))) {
            log.info("CGLibProxy- intercept- before");
            Object result = methodProxy.invokeSuper(proxy, args);
            log.info("CGLibProxy- intercept- after");
            return result;
        }
        return methodProxy.invokeSuper(proxy, args);
    }

}

    public static void main(String[] args) {
        EClass eClass = new EClass();
        CGLibProxy proxy = new CGLibProxy(eClass);
        proxy.createProxy().execute();
    }
```

CGLibProxy代理类需要实现一个方法拦截器接口`MethodInterceptor`并重写`intercept`方法，类似JDK动态代理的`InvocationHandler`接口，也是理解为回调函数，同理每次调用代理对象的方法时，`intercept`方法都会被调用，利用该方法便可以在运行时对方法执行前后进行动态增强。代理对象创建则通过`Enhancer`类来设置的，`Enhancer`是一个用于产生代理对象的类，作用类似JDK的Proxy类，因为CGLib底层是通过继承实现的动态代理，因此需要传递目标对象的Class，同时需要设置一个回调函数对调用方法进行拦截并进行相应处理，最后通过create()创建目标对象的代理对象。

在Spring AOP中，当有接口时会自动选择JDK动态代理技术，如果没有则选择CGLIB技术。

## AspectJ 与 Spring AOP对比

AspectJ和Spring AOP都是对目标类增强，生成代理类。

AspectJ是编译期增强，而SpringAOP是运行时增强。

AspectJ是在编译期间将切面代码编译到目标代码的，属于静态代理；Spring AOP是在运行期间通过代理生成目标类，属于动态代理。

AspectJ是静态代理，故而能够切入final修饰的类，abstract修饰的类；Spring AOP是动态代理，其实现原理是通过CGLIB生成一个继承了目标类(委托类)的代理类，因此，final修饰的类不能被代理，同样static和final修饰的方法也不会代理，因为static和final方法是不能被覆盖的。在CGLIB底层，其实是借助了ASM这个非常强大的Java字节码生成框架。关于CGLB和ASM的讨论将会新开一个篇幅探讨。

Spring AOP支持注解，在使用@Aspect注解创建和配置切面时将更加方便。而使用AspectJ，需要通过.aj文件来创建切面，并且需要使用ajc（Aspect编译器）来编译代码。

## SpringBoot 中使用AOP

### 引入依赖

```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### 使用Aspect注解实现切面

```java
@org.aspectj.lang.annotation.Aspect
@Component
@Slf4j
public class Aspect {

    private static final Gson gson = new Gson();

    @Pointcut("execution(public * com.nopainanymore.deepinspring.AOP.AOPController.*(..)))")
    public void pointCut() {
    }

    @Before("pointCut()")
    public void doBefore(JoinPoint joinPoint) {
        ServletRequestAttributes requestAttributes = (ServletRequestAttributes) RequestContextHolder.currentRequestAttributes();
        log.info("Aspect- doBefore:{}", gson.toJson(requestAttributes.getRequest().getRequestedSessionId()));
        log.info("Aspect- doBefore:{}", joinPoint.toShortString());
        log.info("Aspect- doBefore- before the pointCut");
    }

    @After("pointCut()")
    public void doAfter() {
        log.info("Aspect- doAfter- after the pointCut");
    }

    @AfterReturning(pointcut = "pointCut()", returning = "ret")
    public void doAfterReturning(String ret) {
        log.info("Aspect- doAfterReturning:{}", ret);
    }


    @AfterThrowing(pointcut = "pointCut()")
    public void doAfterThrowing() {
        log.info("Aspect- doAfterThrowing- Exception");
    }

    @Around("pointCut()")
    public String doAround(ProceedingJoinPoint proceedingJoinPoint) {
        log.info("Aspect- doAround- start");
        try {
            return (String) proceedingJoinPoint.proceed();
        } catch (Throwable throwable) {
            log.info("Aspect- doAround- end");
            return throwable.getMessage();
        }
    }
}

```

### 切面作用目标

```java
@RestController
@RequestMapping("/aop")
@Slf4j
public class AOPController {


    @GetMapping("/hello")
    public String aop() {
        log.info("AOPController- aop: hello AOP ");
        return "AOP";
    }

}
```

### 切入点指示符

用于定义匹配方法通知到目标方法的规则。

#### 通配符

符号|作用
-|-
*|匹配任意数量的字符
..|匹配方法定义中的任意数量的参数，此外还匹配类定义中的任意数量包
+|匹配给定类的任意子类

#### 类型签名表达式

`thin(<type name>)`

#### 方法签名表达式

```java
//scope ：方法作用域，如public,private,protect
//returnt-type：方法返回值类型
//fully-qualified-class-name：方法所在类的完全限定名称
//parameters 方法参数
execution(<scope> <return-type> <fully-qualified-class-name>.*(parameters))
```

#### 其他指示符

指示符|作用
-|-
bean|用于匹配特定名称的Bean对象的执行方法
this |用于匹配当前AOP代理对象类型的执行方法；
target |用于匹配当前目标对象类型的执行方法；
@within|用于匹配所以持有指定注解类型内的方法；请注意与within是有区别的， within是用于匹配指定类型内的方法执行；
@annotation|根据所应用的注解进行方法过滤

> 指示符可以使用运算符语法进行表达式的混编，如and、or、not（或者&&、||、！）

## 参考资料

<http://www.importnew.com/24305.html>
<https://blog.csdn.net/javazejian/article/details/56267036>
<https://github.com/landy8530/DesignPatterns#231-%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8F%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F>
<https://blog.csdn.net/yhl_jxy/article/details/80586785>
<http://blog.didispace.com/springbootaoplog/>
