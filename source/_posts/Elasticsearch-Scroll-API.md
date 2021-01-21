---
title: Elasticsearch Search Scroll API
tags: [Elasticsearch,Scroll]
categories:
- Elasticsearch
- Java High Level Rest Api
- Search Scroll
date: 2019-10-12 10:32:08
entitle: Elasticsearch-SearchScroll-API
---

Elasticsearch SearchScroll 笔记。

<!--more-->

Elasticsearch SearchScroll 可以用在查询数据量较大的情况。

## initialize the search scroll context

要使用Scroll必须在使用`Search API`时初始化`Scroll Session`，当ES处理SearchRequest的时候，将会检测到 scroll参数并且保持search context 在相应的时间范围内存活。

```java
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
    searchSourceBuilder.query(matchQuery("title", "Elasticsearch"));
    searchSourceBuilder.size(size);
    searchRequest.source(searchSourceBuilder);
    searchRequest.scroll(TimeValue.timeValueMinutes(1L));
    SearchResponse searchResponse = client.search(searchRequest, RequestOptions.DEFAULT);
    String scrollId = searchResponse.getScrollId();
    SearchHits hits = searchResponse.getHits();
```

## Retrieve all the relevent documents

在新的scroll间隔，ScrollId必须通过searchScroll方法设置到SearchScrollRequest，ES将返回下一批查询结果和一个新的ScrollId，以此类推。


```java
    SearchScrollRequest scrollRequest = new SearchScrollRequest(scrollId);
    scrollRequest.scroll(TimeValue.timeValueSeconds(30));
    SearchResponse searchScrollResponse = client.scroll(scrollRequest, RequestOptions.DEFAULT);
    scrollId = searchScrollResponse.getScrollId();
    hits = searchScrollResponse.getHits();
    assertEquals(3, hits.getTotalHits().value);
    assertEquals(1, hits.getHits().length);
    assertNotNull(scrollId);
```

## Clear the scroll context

最后ScrollId可以通过ClearScrollAPI进行删除，以释放查询的上下文。当Scroll超时时ScrollId可以自动清除，但是最佳实践是当srcoll session完成时尽快清除。
