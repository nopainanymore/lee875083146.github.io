---
title: DSL-Query
tags: [Elasticsearch,DSL,Query]
categories:
- Elasticsearch
- DSL
- Query
date: 2019-08-19 09:19:30
entitle: Elasticsearch-DSL-Query
---

最近在使用ES，对HighLevelRestClient稍微进行下封装，因为之前从来没有使用过ES，先学习下ES的基础知识以及查询语句。本篇主要介绍ES的查询。

<!--more-->

## DSL-Query

### mach_all

匹配所有document，常与filter结合使用
```
GET /_search
{
    "query": {
        "match_all": {}
    }
}
```

### mach

适用于全文搜索和精确查询：
*  全文字段：分词后去查询
*  精确字段：精确匹配给定值

> 对于精确查找可能需要使用filter语句来取代query，因为filter将会被缓存。

```
GET /_search
{
    "query": {
        "match": {
            "tweet": "elasticsearch"
        }
    }
}
```

### muti_match

可以在多个字段上执行相同的match查询。

```
{
    "multi_match": {
        "query":    "full text search",
        "fields":   [ "title", "body" ]
    }
}
```

### range查询

查询出落在指定区间内的数字或者时间。

操作符|含义
-|-
gt|大于
gte|大于等于
lt|小于
lte|小于等于

```
{
    "range": {
        "age": {
            "gte":  20,
            "lt":   30
        }
    }
}
```

### term

用于精确值匹配，精确值可能是数字、时间、布尔或者`not_analyzed`的字符串。

```
{ "term": { "age":    26           }}
{ "term": { "date":   "2014-09-01" }}
{ "term": { "public": true         }}
{ "term": { "tag":    "full_text"  }}
```

### terms

与term相同，但允许多值匹配，如果字段中包含了指定值的任何一个值，认为该document满足条件。

```
{ "terms": { "tag": [ "search", "full_text", "nosql" ] }}
```
### exist和missing

用于查找那些指定字段中有值 `exists`或无值`missing`的文档。经常用于某个字段有值的情况和某个字段缺值的情况。

```
{
    "exists":   {
        "field":    "title"
    }
}
```

### 组合多查询

### bool查询

通过`bool`查询将多查询组合在一起。
参数|含义
-|-
must|文档**必须匹配**这些条件才能被包含进来。
must_not|文档**必须不匹配**这些条件才能被包含进来。
should|如果满足这些语句中的任意语句，将增加 `_score`，否则，无任何影响。它们主要用于修正每个文档的相关性得分。
filter|**必须匹配**，但它以不评分、过滤模式来进行。这些语句对评分没有贡献，只是根据过滤标准来排除或包含文档。

```
{
    "bool": {
        "must":     { "match": { "title": "how to make millions" }},
        "must_not": { "match": { "tag":   "spam" }},
        "should": [
            { "match": { "tag": "starred" }},
            { "range": { "date": { "gte": "2014-01-01" }}}
        ]
    }
}
```

### constant_score查询

将一个不变的常亮评分应用于所有匹配的文档。常用于只需要执行一个filter而没有其他查询的情况下。

可以使用它来取代只有 filter 语句的 bool 查询。在性能上是完全相同的，但对于提高查询简洁性和清晰度有很大帮助。

term 查询被放置在 constant_score 中，转成不评分的 filter。这种方式可以用来取代只有 filter 语句的 bool 查询。

### 验证查询

查询可以变得非常的复杂，尤其 和不同的分析器与不同的字段映射结合时，理解起来就有点困难了。不过 validate-query API 可以用来验证查询是否合法。

将 explain 参数 加到查询字符串中可以找出查询不合法的原因。

## 排序与相关性

### 按照字段的值排序

通过`sort`参数实现。

```
GET /_search
{
    "query" : {
        "bool" : {
            "filter" : { "term" : { "user_id" : 1 }}
        }
    },
    "sort": { "date": { "order": "desc" }}
}
```

### 多级排序

排序条件的顺序是很重要的。结果首先按第一个条件排序，仅当结果集的第一个 sort 值完全相同时才会按照第二个条件进行排序，以此类推。

多级排序并不一定包含` _score` 你可以根据一些不同的字段进行排序， 如地理距离或是脚本计算的特定值。

```
GET /\_search
{
    "query" : {
        "bool" : {
            "must":   { "match": { "tweet": "manage text search" }},
            "filter" : { "term" : { "user_id" : 2 }}
        }
    },
    "sort": [
        { "date":   { "order": "desc" }},
        { "_score": { "order": "desc" }}
    ]
}
```

### 多值字段的排序

一种情形是字段有多个值的排序， 需要记住这些值并没有固有的顺序；一个多值的字段仅仅是多个值的包装，这时应该选择哪个进行排序呢？

对于数字或日期，你可以将多值字段减为单值，这可以通过使用 min 、 max 、 avg 或是 sum 排序模式 。

```
"sort": {
    "dates": {
        "order": "asc",
        "mode":  "min"
    }
}
```
