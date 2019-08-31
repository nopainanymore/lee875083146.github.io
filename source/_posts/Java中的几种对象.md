---
title: Java中的几种对象
tags: [Java,POJO,PO,VO,DTO,AO,BO]
categories:
- JAVA
- POJO
date: 2019-08-16 22:16:19
entitle: Java-POJO
---

介绍Java中的POJO对象，
<!--more-->

## POJO的起源

POJO——Plain Old Java Object，是一个简单普通的Java对象，不被任意特殊的限制所约束，并且与类路径无关。

>In software engineering, a Plain Old Java Object (POJO) is an ordinary Java object, not bound by any special restriction and not requiring any class path.

这个概念出自MartinFowler博客。该术语是在他与Rebecca Parsons、Josh MacKenzie讨论的过程中产生的。在讨论中，他们指出将业务逻辑赋予给Java对象比使用实体Bean有更多的好处。他们好奇为什么人们反对在系统中使用普通的对象，归结为简单对象缺少一个时髦的名字，所以他们给了一个名字出来，结果非常的好。

><https://www.martinfowler.com/bliki/POJO.html>
>The term was coined while Rebecca Parsons, Josh MacKenzie and I were preparing for a talk at a conference in September 2000. In the talk we were pointing out the many benefits of encoding business logic into regular java objects rather than using Entity Beans. We wondered why people were so against using regular objects in their systems and concluded that it was because simple objects lacked a fancy name. So we gave them one, and it's caught on very nicely.

以下是维基百科中关于POJO的定义：
1. POJO不能继承预先制定的别的类
2. POJO不能实现预先制定的接口
3. POJO不能被预先制定的注解标注

但是由于技术困难和其他一些原因，一些被称为符合POJO的软件产品或者框架仍然需要为了某些特性使用预先指定的一些注解，例如持久化等功能。所以如果在被任何注解注释前一个类是一个POJO对象，并且这个类在移除注解之后仍然能够被视作一个POJO。那么基本对象仍是POJO，因为它没有特殊的特性使其成为专用的Java对象。
><https://en.wikipedia.org/wiki/Plain_old_Java_object>
>Ideally speaking, a POJO is a Java object not bound by any restriction other than those forced >by the Java Language Specification; i.e. a POJO should **not** have to
>Extend prespecified classes, as in
>```
>public class Foo extends javax.servlet.http.HttpServlet { ...
>```
>Implement prespecified interfaces, as in
>```
>public class Bar implements javax.ejb.EntityBean { ...
>```
>Contain prespecified annotations, as in
>```
>@javax.persistence.Entity public class Baz { ...
>```
>
However, due to technical difficulties and other reasons, many software products or frameworks described as POJO-compliant actually still require the use of prespecified annotations for features such as persistence to work properly. The idea is that if the object (actually class) was a POJO before any annotations were added, and would return to POJO status if the annotations are removed then it can still be considered a POJO. Then the basic object remains a POJO in that it has no special characteristics (such as an implemented interface) that makes it a "Specialized Java Object" (SJO or (sic) SoJO).


## PO

PO——Persistant Object，用于表示数据库中的一条记录映射成的Java对象，PO仅仅用于表示数据，没有任何的数据操作。

## VO

VO——Value Object，用于表示一个与前端交互的Java对象，VO中不包含后段的敏感数据，例如数据库的自增字段等。

## DTO

DTO——Data Transfer Object，用于表示一个数据传输对象，DTO一般用于不同服务层之间传输数据。

## BO

BO——Business Object，用于表示一个业务对象，BO中包括了业务逻辑，常常封装了对DAO、RPC等的调用。

## 总结

对于这些对象，个人认为没有必要去纠结，更多的应该对业务本身进行分析建模，划分业务中的对象。



## 参考资料
<https://en.wikipedia.org/wiki/Plain_old_Java_object>
<https://www.cnblogs.com/firstdream/archive/2012/04/13/2445582.html>
<https://www.zhihu.com/question/39651928>
