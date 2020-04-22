# Search APIs

大多数搜索API都是多索引的，除了Explain API。

## Routing

执行搜索时, Elasticsearch会基于**adaptive replica selection**公式来选择最佳数据副本.
还可以通过`routing`参数来指定搜索哪些分片.
例如当索引tweets时, routing的值可以是user:

```http
POST /twitter/_doc?routing=kimchy
{
    "user" : "kimchy",
    "postDate" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
```

在这个情况下, 如果我们想要在twitter中搜索指定用户, 
我们可以指定user作为routing参数, 从而导致只搜索相关的分片:

```http
POST /twitter/_search?routing=kimchy
{
    "query": {
        "bool" : {
            "must" : {
                "query_string" : {
                    "query" : "some query string here"
                }
            },
            "filter" : {
                "term" : { "user" : "kimchy" }
            }
        }
    }
}
```

routing参数可以是多值, 通过`,`逗号分隔.
它会导致命中匹配routing参数的相关分片.

## Adaptive Replica Selectionedit

默认情况下, Elasticsearch会使用被称为`adaptive replica selection`的公式.
基于下面的一些条件, 允许相关节点发送请求到被认为是最好的数据副本:

- 其他节点和最好数据副本所在节点的响应时间
- 查询请求在包含数据的节点上所花费的执行时间
- 在包含数据的节点上的查询用线程池的队列大小

可以通过修改集群设置参数`cluster.routing.use_adaptive_replica_selection`为false来关闭adaptive replica selection.

```http
PUT /_cluster/settings
{
    "transient": {
        "cluster.routing.use_adaptive_replica_selection": false
    }
}
```

如果adaptive replica selection被关闭, 查询请求将会被发送给在所有数据副本(主节点和备份节点)的所有索引分片.

## Stats Groups

搜索可以和stats groups组合, 它们为没有一个group维护了一个统计聚合.
它可以通过[indices stats][] API来获取. 例如下面是一个搜索请求体中关联了两个不同的stat groups.

```http
POST /_search
{
    "query" : {
        "match_all" : {}
    },
    "stats" : ["group1", "group2"]
}
```

## Global Search Timeout

每个独立的搜索可以在[Request Body Search][]中设置超时时间.
由于搜索请求可以源自多个源，因此Elasticsearch具有全局搜索超时的动态集群级设置，
该设置适用于未在请求正文中设置超时的所有搜索请求。
这些请求会在一定时间后被取消, 根据下一节Search Cancellation中描述的原理. 
因此关于超时的相同的警告是被允许的.

通过[Cluster Update Settings][]来设置全局超时时间search.default_search_timeout.
默认没有设置全局超时时间, 如果设置为-1则永远不超时.

## Search Cancellation

TODO

## Search concurrency and parallelism

TODO

---
[indices stats]: https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-stats.html
[Request Body Search]: https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html
[Cluster Update Settings]: https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-update-settings.html