---
title: DSL-Aggregation
tags: [Elasticsearch,DSL,Aggregation]
categories:
- Elasticsearch
- DSL
- Aggregation
date: 2019-08-19 10:06:42
entitle: Elasticsearch-DSL-Aggregation
---

DSL中聚合的基础概念。
<!--more-->

## 基础概念

### 桶

满足特定条件的文档的集合。
聚合开始执行的时候，每个文档里面的值通过计算来决定符合哪个桶的条件。如果匹配到，文档将放入相应的桶并接着进行聚合操作。
桶也可以被嵌套在其他桶里面，提供层次化的或者有条件的划分方案。

### 指标

桶能让我们划分文档到有意义的集合， 但是最终我们需要的是对这些桶内的文档进行一些指标的计算。分桶是一种达到目的的手段：它提供了一种给文档分组的方法来让我们可以计算感兴趣的指标。

### 聚合

聚合是由桶和指标组成的。 聚合可能只有一个桶，可能只有一个指标，或者可能两个都有。也有可能有一些桶嵌套在其他桶里面。

## 尝试聚合

### 单字段聚合
```
GET /cars/transactions/_search
{
    "size" : 0,
    "aggs" : {
        "popular_colors" : {	①
            "terms" : {		②
              "field" : "color"
            }
        }
    }
}
```
* 聚合操作置于aggs之下
* 聚合可以指定想要的名称
* 定义单个桶的类型terms
* 指定size为0，将返回的记录数设置为0，提高查询的效率

①为聚合起一个名字，响应的结果将以我们定义的名字为标签，由此可以对结果进行解析。
②定义聚合本身，例子中定义了一个单terms桶，这个桶会为每个碰到的唯一词项动态创建新的桶，以color字段为terms，桶会为每个颜色动态创建新桶。

```
{
...
   "hits": {
      "hits": []
   },
   "aggregations": {
      "popular_colors": {
         "buckets": [
            {
               "key": "red",
               "doc_count": 4
            },
            {
               "key": "blue",
               "doc_count": 2
            },
            {
               "key": "green",
               "doc_count": 2
            }
         ]
      }
   }
}
```

* 设置了size参数，不会又hits搜索结果返回
* popular_color聚合作为aggregations字段的一部分被返回的
* 每个桶的数量代表该颜色的文档数量

### 添加度量指标

为了获取复杂的文档度量，我们需要指定度量使用的指标和计算方法，复杂聚合需要将度量嵌套在桶中，度量会基于桶内的文档计算统计结果。

```
GET /cars/transactions/_search
{
   "size" : 0,
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {	①
            "avg_price": {	②
               "avg": {
                  "field": "price"	③
               }
            }
         }
      }
   }
}
```

* ①为度量新增aggs层
* ②为度量指定名字：avg_price
* ③为price字段定义avg度量

通过上面的请求，新的聚合层将以price为指标avg为计算方法的度量嵌套置于terms桶中，该例中为每个颜色生成了平均价格。

同样可以根据度量的名字获取它的值

```
{
...
   "aggregations": {
      "colors": {
         "buckets": [
            {
               "key": "red",
               "doc_count": 4,
               "avg_price": {	①
                  "value": 32500
               }
            },
            {
               "key": "blue",
               "doc_count": 2,
               "avg_price": {
                  "value": 20000
               }
            },
            {
               "key": "green",
               "doc_count": 2,
               "avg_price": {
                  "value": 21000
               }
            }
         ]
      }
   }
...
}
```

* ①响应字段中的avg_price。


## 嵌套桶

可以将桶嵌套进另外一个桶。

```
GET /cars/transactions/_search
{
   "size" : 0,
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {
            "avg_price": {	①
               "avg": {
                  "field": "price"
               }
            },
            "make": {	②
                "terms": {
                    "field": "make"	③
                }
            }
         }
      }
   }
}
```

* ①第一个度量仍为平均价格
* ②第二个度量被加入到了color桶中
* ③这个聚合为terms桶，为每个make生成唯一的桶

一个聚合的每个层级都可以有多个度量或者桶，a v g_price度量表示每种颜色的车的平均价格，与其他的桶和度量相对独立。

聚合能使我们用一次数据请求获得完全不同的度量需求收集。

 make 聚合是一个 terms 桶（嵌套在 colors 、 terms 桶内）。着它会为数据集中的每个唯一组合生成（ color 、 make ）元组。

```
{
...
   "aggregations": {
      "colors": {
         "buckets": [
            {
               "key": "red",
               "doc_count": 4,
               "make": { ①
                  "buckets": [
                     {
                        "key": "honda", ②
                        "doc_count": 3
                     },
                     {
                        "key": "bmw",
                        "doc_count": 1
                     }
                  ]
               },
               "avg_price": {
                  "value": 32500 ③
               }
            },

...
}

```

* ①新的聚合嵌入在每个颜色的桶中
* ②每种颜色下按照不同生产商分解的车辆信息
* ③avg度量仍然维持不变

##


```
GET /cars/transactions/_search
{
   "size" : 0,
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {
            "avg_price": { "avg": { "field": "price" }
            },
            "make" : {
                "terms" : {
                    "field" : "make"
                },
                "aggs" : { ①
                    "min_price" : { "min": { "field": "price"} }, ②
                    "max_price" : { "max": { "field": "price"} } ③
                }
            }
         }
      }
   }
}

```

* ①增加一个新的aggs嵌套层级
* ②包括min最小度量
* ③包括max最大度量

```
{
...
   "aggregations": {
      "colors": {
         "buckets": [
            {
               "key": "red",
               "doc_count": 4,
               "make": {
                  "buckets": [
                     {
                        "key": "honda",
                        "doc_count": 3,
                        "min_price": {
                           "value": 10000
                        },
                        "max_price": {
                           "value": 20000
                        }
                     },
                     {
                        "key": "bmw",
                        "doc_count": 1,
                        "min_price": {
                           "value": 80000
                        },
                        "max_price": {
                           "value": 80000
                        }
                     }
                  ]
               },
               "avg_price": {
                  "value": 32500
               }
            },
...
```
