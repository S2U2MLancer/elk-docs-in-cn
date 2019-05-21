# Alias datatype

**NOTE**: 

只能在具有**单一映射类型**的索引上指定字段别名。 
要添加字段别名，索引必须已在6.0或更高版本中创建，或者是设置为index.mapping.single_type：true的旧索引。 
有关更多信息，请参阅[Removal of mapping types][]。

别名映射定义索引中字段的备用名称。 别名可用于代替搜索请求中的目标字段，以及选择其他API（如字段功能）。
```
PUT trips
{
  "mappings": {
    "_doc": {
      "properties": {
        "distance": {
          "type": "long"
        },
        "route_length_miles": {
          "type": "alias",
          "path": "distance" // 1
        },
        "transit_mode": {
          "type": "keyword"
        }
      }
    }
  }
}

GET _search
{
  "query": {
    "range" : {
      "route_length_miles" : {
        "gte" : 39
      }
    }
  }
}
```

1. 目标字段的路径。 请注意，这必须是完整路径，包括任何父对象（例如object1.object2.field）。

搜索请求的几乎所有组件都接受字段别名。 
特别是，别名可用于查询，聚合和排序字段，
以及请求docvalue_fields，stored_fields，suggestions和highlights。 
访问字段值时，脚本还支持别名。 有关不支持部分，请参阅[unsupported APIs][]。

在搜索请求的某些部分以及请求字段功能时，可以提供字段通配符模式。 在这些情况下，除了具体字段之外，通配符模式还将匹配字段别名：
```
GET trips/_field_caps?fields=route_*,transit_mode
```

## Alias targets

别名的目标有一些限制：

- 目标必须是具体字段，而不是对象或其他字段别名。
- 目标字段必须在创建别名时存在。
- 如果定义了嵌套对象，则字段别名必须与其目标具有相同的嵌套范围。

此外，字段别名只能有一个目标。 这意味着无法使用字段别名来查询单个子句中的多个目标字段。

## Unsupported APIsedit

**不支持写入字段别名**：尝试在索引或更新请求中使用别名将导致失败。 
同样，别名不能用作copy_to的目标或多字段。

由于文档源中不存在别名，因此在执行源过滤时不能使用别名。 例如，以下请求将返回_source的空结果：
```
GET /_search
{
  "query" : {
    "match_all": {}
  },
  "_source": "route_length_miles"
}
```

目前，只有搜索和field capabilities API才会接受并解析字段别名。 其他接受字段名称的API（例如术语向量）不能与字段别名一起使用。

最后，一些查询（例如术语，geo_shape和more_like_this）允许从索引文档中获取查询信息。 
由于在获取文档时不支持字段别名，因此指定查找路径的查询部分不能通过其别名引用字段。

---
[Removal of mapping types]: https://www.elastic.co/guide/en/elasticsearch/reference/current/removal-of-types.html
[unsupported APIs]: https://www.elastic.co/guide/en/elasticsearch/reference/current/alias.html#unsupported-apis
