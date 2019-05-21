# List All Indices

现在让我们来看看我们的索引：
```
GET /_cat/indices?v
```
并返回:
```
health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
```
这仅仅意味着我们在集群中还没有索引。