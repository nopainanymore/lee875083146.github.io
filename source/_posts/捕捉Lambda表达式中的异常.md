---
title: 捕捉Lambda表达式中的异常
entitle: Catch-Exceprion-In-Lambda
tags: [Java8,Lambda,异常处理]
categories:
- Java
- Java8
- Lambda
date: 2019-05-08 23:37:49
---

Lambda中捕捉异常，会导致代码的膨胀，违反了Lambda设计的原则，同时也是Java FunctionalInterface设计的缺陷。
<!--more-->
## 在Lambad中捕捉异常

```java
  List<Class> classList = new ArrayList<>();
        List<String> classNameList = Arrays.asList("Integer", "Double", "A");
        classList = classNameList
                .stream()
                .map(className -> {
                    try {
                        return Class.forName(className);
                    } catch (ClassNotFoundException e) {
                        e.printStackTrace();
                        return null;
                    }
                })
                .collect(Collectors.toList());
```




## 引入工具类
```java
public final class LambdaExceptionUtil {

    @FunctionalInterface
    public interface ConsumerWithExceptions<T, E extends Exception> {
        void accept(T t) throws E;
    }

    @FunctionalInterface
    public interface BiConsumerWithExceptions<T, U, E extends Exception> {
        void accept(T t, U u) throws E;
    }

    @FunctionalInterface
    public interface FunctionWithExceptions<T, R, E extends Exception> {
        R apply(T t) throws E;
    }

    @FunctionalInterface
    public interface SupplierWithExceptions<T, E extends Exception> {
        T get() throws E;
    }

    @FunctionalInterface
    public interface RunnableWithExceptions<E extends Exception> {
        void run() throws E;
    }

    /** .forEach(rethrowConsumer(name -> System.out.println(Class.forName(name)))); or .forEach(rethrowConsumer(ClassNameUtil::println)); */
    public static <T, E extends Exception> Consumer<T> rethrowConsumer(ConsumerWithExceptions<T, E> consumer) throws E {
        return t -> {
            try { consumer.accept(t); }
            catch (Exception exception) { throwAsUnchecked(exception); }
        };
    }

    public static <T, U, E extends Exception> BiConsumer<T, U> rethrowBiConsumer(BiConsumerWithExceptions<T, U, E> biConsumer) throws E {
        return (t, u) -> {
            try { biConsumer.accept(t, u); }
            catch (Exception exception) { throwAsUnchecked(exception); }
        };
    }

    /** .map(rethrowFunction(name -> Class.forName(name))) or .map(rethrowFunction(Class::forName)) */
    public static <T, R, E extends Exception> Function<T, R> rethrowFunction(FunctionWithExceptions<T, R, E> function) throws E {
        return t -> {
            try { return function.apply(t); }
            catch (Exception exception) { throwAsUnchecked(exception); return null; }
        };
    }

    /** rethrowSupplier(() -> new StringJoiner(new String(new byte[]{77, 97, 114, 107}, "UTF-8"))), */
    public static <T, E extends Exception> Supplier<T> rethrowSupplier(SupplierWithExceptions<T, E> function) throws E {
        return () -> {
            try { return function.get(); }
            catch (Exception exception) { throwAsUnchecked(exception); return null; }
        };
    }

    /** uncheck(() -> Class.forName("xxx")); */
    public static void uncheck(RunnableWithExceptions t)
    {
        try { t.run(); }
        catch (Exception exception) { throwAsUnchecked(exception); }
    }

    /** uncheck(() -> Class.forName("xxx")); */
    public static <R, E extends Exception> R uncheck(SupplierWithExceptions<R, E> supplier)
    {
        try { return supplier.get(); }
        catch (Exception exception) { throwAsUnchecked(exception); return null; }
    }

    /** uncheck(Class::forName, "xxx"); */
    public static <T, R, E extends Exception> R uncheck(FunctionWithExceptions<T, R, E> function, T t) {
        try { return function.apply(t); }
        catch (Exception exception) { throwAsUnchecked(exception); return null; }
    }

    @SuppressWarnings ("unchecked")
    private static <E extends Throwable> void throwAsUnchecked(Exception exception) throws E { throw (E)exception; }

}
```

## 如何使用

引入工具类之前：
![LambdaExceptionBefore](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/java8/LambdaExceptionBefore.png?x-oss-process=style/sw-white "LambdaExceptionBefore")

引入工具类之后：
![LambdaExceptionAfter](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/java8/LambdaExceptionAfter.png?x-oss-process=style/sw-white "LambdaExceptionAfter")

代码：
```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

/**
 * java8: LambdaExceptionExample
 *
 * @author NoPainAnymore
 * @date 2019-05-13 21:53
 */
public class LambdaExceptionExample {

    public static void main(String[] args) throws ClassNotFoundException {

        List<String> clazzNameList = new ArrayList<>();
        clazzNameList.add("class0");
        clazzNameList.add("class1");

        List<? extends Class<?>> collect = clazzNameList.stream().map(clazz -> Class.forName(clazz)).collect(Collectors.toList());

        List<? extends Class<?>> collect1 = clazzNameList.stream().map(LambdaExceptionUtil.rethrowFunction(clazz -> Class.forName(clazz))).collect(Collectors.toList());
        

    }
}

```