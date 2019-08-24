---
title: CleanCode——代码臃肿
entitle: CleanCode-Bloated
tags: [CleanCode,代码臃肿]
categories:
- CleanCode
- 代码臃肿
date: 2019-06-02 15:31:13
---

Bloated——代码臃肿，即代码中的类、函数、字段没有经过合理的组织，而是简单的堆砌在一起，随着代码规模的不断增长，臃肿日益累积，将会使代码维护性、可读性降低。

<!--more-->
## Primitive Obsenssion
在领域建模的时候，将目标事物抽象成类，类则由基本数据类型的成员变量组成，当随着对目标事物的深入或者需求的改变时，需要通过成员变量的增加来实现，随之成员变量变得不易管理。
当成员变量数量成规模后，将他们有组织的结合形成新的类，可以更方便的管理这些数据，使代码变得更加灵活，提高代码的可读性和可维护性。集中的数据处理使内聚性得以保证。
### 解决方法
* Replace Type code with Class——以类取代类型码，将成员变量中存在逻辑联系的字段组织起来并抽取成一个类，使用组合的方式，使抽取的类与原有的类结合成新的类，注意形成的新类中存在抽取类数据的关联方法。
* Introduce Parameter Object——引入参数对象、Preserve Whole Object——保持对象完整。
* 如果想要替换的成员变量是表示类型的变量，且并不影响行为，则可以运用Replace Type Code with Class——以类取代类型码 将其替换。如果有与类型码相关的条件表达式，可以运用Repalace Type Code with Subclass——以子类取代类型码或者Replace Type Code with State/Strategy——以状态/策略模式取代类型码加以处理。
* 如果是从数组中挑选数据，则可以运用Replace Array with Object。
### 方法说明
#### Replace Type Code with Class
使用新的类来替换类型字段
#### Introduce Parameter Object
将函数入参替换成参数对象。
#### Preserve Whole Object
函数参数由某一个对象的若干字段值组成，使用整个对象作为参数。
#### Replace Type Code with Subclass
如果类中存在某一字段会影响类的行为，将该字段消除，以子类来取代。
#### Replace Type Code with State/Strategy
如果类中存在某一字段会影响类的行为，使用子类无法消除，则使用状态模式或者策略模式来替代。
#### Replace Array with Object
数组中个各元素表示不同的对象，则使用对象来替代这个数组。

## Data Clumps
代码的不同部分包含相同的变量组。这些绑在一起出现的数据应该拥有自己的对象。
这种问题一般出现在对对象的领域划分不够明确、或者代码习惯不够好。
应该将拥有共同意义的数据抽取成为一个对象。可以提高代码易读性和组织性。对于特殊数据的操作，可以集中处理。
### 解决方法
* 找到数据以字段形式出现的地方，运用Extract Class——提炼类将它们提炼到一个独立对象中。
* 如果这些字段在函数参数中出现，运用Introduce Parameter Object——引入参数对象将它们组织成一个类。
* 若果这些字段出现在其他函数中，运用Perserve Whole Object——保持对象完整将整个数据对象传入到函数中。
### 方法说明
#### Extract Class
根据职责单一原则将同一领域范围的字段提取成类。

## Large Class
一个类有过多的字段、函数、代码行。
根据职责单一原则，一个类只赋予它一个职责。可以保持类的内聚性、避免代码和功能的重复。
### 解决方法
* 如果大类中的部分行为可以提炼到一个独立的组件中，可以使用Extract Class——提炼类。
* 如果大类中的部分行未可以使用不同的方法实现或者用于特殊场景，可以使用Extract Subclass——提炼子类。
* 如果有必要为客户端提供一组操作和行为，可以使用Extract Interface——提炼接口。

### 方法说明
#### Extract Interface
多个客户端使用一个类相同的函数，或者相近似的类拥有相同的方法，将这些方法移动到接口中。


## Long Method
一个函数含有太多的代码。
这种情况出现在代码经过很多人的修改，没有适当的抽象提取，函数变得膨胀。
在面向对象的语言中，函数比较短小精悍的类往往生命周期比较长，合适的抽象可以增强代码的可重用率，且易于维护和理解。
### 解决方法
* Extract Method——提炼函数
* 如果局部变量和参数干扰提炼函数，可以使用Replace Temp with Query——以查询取代临时变量，Introduce Parameter Object——引入参数对象或者Preserve Whole Object——保持对象完整。
* 如果前两条没有帮助，则可以使用Repalce Method with Method Object——以函数对象取代函数，尝试移动整个函数到一个独立的对象中。
* 条件表达式和循环常常也是提炼的信号，对于条件表达式，可以使用Decompose Conditional——分解条件表达式，循环应该使用Extract Method——提炼函数，将整个循环提炼到独立的函数中。
### 方法说明
#### Extract Method
将可以组织在一起的代码抽取成为一个函数。
#### Replace Temp with Query
将查询或者计算的结果放在局部变量中，在代码中使用。
#### Replace Method with Method Object
当函数过长且局部变量交织在一起，无法使用提炼函数，将其提炼到一个独立的类中，使局部变量变成心类的成员变量，再对函数进行分割。
#### Decompose Conditional
将复杂的表达式使用函数替代。


## Long Parameter List
一个函数有超过3、4个参数。
过长参数列可能是将多个算法并到一个函数中时发生的。函数中的入参用来控制选用哪个算法去执行。
过长参数列也可能是解耦类之间依赖关系时的副产品。例如，用于创建函数中所需的特定对象的代码已从函数移动到调用函数的代码处，但创建的对象是作为参数传递到函数中。因此，原始类不再知道对象之间的关系，并且依赖性也已经减少。但是如果创建的这些对象，每一个都将需要它自己的参数，这意味着过长参数列。
太长的参数列难以理解，太多参数会造成前后不一致、不易使用，而且一旦需要更多数据，就必须修改它。
### 解决方法
* 如果可以使用单独的函数取代参数，那么你应该使用Replace Parameter with Methods——以函数取代参数。
* 使用Preserve Whole Object)——保持对象完整将来自同一对象的一堆数据收集起来，并以该对象替换它们。
* 如果某些数据缺乏合理的对象归属，使用Introduce Parameter Object——引入参数对象为它们制造出一个“参数对象”。
### 方法说明
#### Replace Parameter with Methods
对象调用某个函数，并将所得结果作为参数，传递给另一个函数。而接受该参数的函数本身也能够调用前一个函数。让参数接受者去除该项参数，并直接调用前一个函数。



## 参考资料
[代码坏味道之代码臃肿](https://github.com/dunwu/blog/blob/master/source/_posts/design/refactor/%E4%BB%A3%E7%A0%81%E5%9D%8F%E5%91%B3%E9%81%93%E4%B9%8B%E4%BB%A3%E7%A0%81%E8%87%83%E8%82%BF.md)