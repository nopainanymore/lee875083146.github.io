---
title: Java中的几种对象
tags: [Java,POJO,PO,VO,DTO,AO,BO,DAO]
categories:
- JAVA
- POJO
date: 2019-08-16 22:16:19
entitle: Java-POJO
---

介绍Java中的POJO对象，
<!--more-->




## POJO的起源


POJO——Plain Old Java Object，
><https://www.martinfowler.com/bliki/POJO.html>
>The term was coined while Rebecca Parsons, Josh MacKenzie and I were preparing for a talk at a conference in September 2000. In the talk we were pointing out the many benefits of encoding business logic into regular java objects rather than using Entity Beans. We wondered why people were so against using regular objects in their systems and concluded that it was because simple objects lacked a fancy name. So we gave them one, and it's caught on very nicely.

>
>Ideally speaking, a POJO is a Java object not bound by any restriction other than those forced >by the Java Language Specification; i.e. a POJO should not have to
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

## DAO

## VO

## DTO

## BO



## 参考资料
<https://en.wikipedia.org/wiki/Plain_old_Java_object>
<https://www.cnblogs.com/firstdream/archive/2012/04/13/2445582.html>

