# Create Index

Create Index API用于在Elasticsearch中手动创建索引。 Elasticsearch中的所有文档都存储在一个索引或另一个索引中。

最基本的命令如下：
```
PUT twitter
```

这将创建一个名为twitter的索引，其所有的设置都使用默认值。

**NOTE**: Index name limitations

索引的命名有几个限制。 完整的限制列表如下：

- 只能小写
- 不能使用\, /, *, ?, ", <, >, |, ** **(空格符), **,**(逗号), #
- 7.0之前的索引可能包含冒号（:)，但已被弃用，7.0+不支持
- 不能以 -，_，+开头
- 不可以为.或者..
- 不能超过**255个字节**（注意它是字节，因此多字节字符将更快地计入255个限制）

---

## Index Settings

可以为将要创建的索引关联一些指定的参数，在正文中定义：
```
PUT twitter
{
    "settings" : {
        "index" : {
            "number_of_shards" : 3, 
            "number_of_replicas" : 2 
        }
    }
}
```

- number_of_shards的默认值为5
- number_of_replicas的默认值为1 (意味着**每一个**主分片都有一个副本)

或更简化:
```
PUT twitter
{
    "settings" : {
        "number_of_shards" : 3,
        "number_of_replicas" : 2
    }
}
```
**NOTE**: 您不必在设置中明确指定索引属性部分。

有关创建索引时可以设置的所有不同索引级别设置的更多信息，请查看[index modules][]。

## Mappings

create index API允许提供类型映射：
```
PUT test
{
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        "_doc" : {
            "properties" : {
                "field1" : { "type" : "text" }
            }
        }
    }
}
```

## Aliases

create index API还允许提供一组[alias][]：
```
PUT test
{
    "aliases" : {
        "alias_1" : {},
        "alias_2" : {
            "filter" : {
                "term" : {"user" : "kimchy" }
            },
            "routing" : "kimchy"
        }
    }
}
```

## Wait For Active Shards

默认情况下，索引创建仅在**每个分片的主备份已启动或请求超时**时才返回对客户端的响应。 索引创建响应将指示发生了什么：
```
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "test"
}
```

acknowledged说明了索引是否在集群中创建成功，而shards_acknowledged说明在超时之前是否为索引中的每个分片启动了必需数量的分片副本。 
请注意，仍然有可能acknowledged或shards_acknowledged为false，但索引创建成功。 这些值仅表示操作是否在超时之前完成。 
如果acknowledged为false，意味着新创建的索引还没有来得及更新集群的状态就超时了, 但它很快就要被创建。 
如果shards_acknowledged为false，意味着在必须数量的分片（默认情况下只是主分片）开始前就超时了，即使集群状态已成功更新以反映新创建的索引（即confirmged = true）。

我们可以更改等待主分片数量的默认值, 通过索引设置index.write.wait_for_active_shards（请注意，更改此设置也会影响所有后续写入操作的wait_for_active_shards值）：

```
PUT test
{
    "settings": {
        "index.write.wait_for_active_shards": "2"
    }
}
```
或者通过请求参数wait_for_active_shards:
```
PUT test?wait_for_active_shards=2
```
A detailed explanation of wait_for_active_shards and its possible values can be found [here][index-wait-for-active-shards].

---

[index modules]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules.html
[alias]: https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html
[index-wait-for-active-shards]: https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html#index-wait-for-active-shards