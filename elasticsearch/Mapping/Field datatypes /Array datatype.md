# Array datatype

在Elasticsearch中，没有专用的数组类型。 默认情况下，任何字段都可以包含零个或多个值，
但是，数组中的所有值必须具有相同的数据类型。 例如：

- 字符串数组: [ "one", "two" ]
- 整数数组: [ 1, 2 ]
- 数组的数组:  [ 1, [ 2, 3 ]], 等价于 [ 1, 2, 3 ]
- 对象数组: [ { "name": "Mary", "age": 12 }, { "name": "John", "age": 10 }]

**NOTE**: Arrays of objects

对象数组不能像您期望的那样工作：您无法独立于数组中的其他对象查询每个对象。 
如果您需要能够执行此操作，则应使用嵌套数据类型而不是object数据类型。

[嵌套数据类型][Nested datatype]中对此进行了更详细的说明。

---

动态添加字段时，数组中的第一个值确定字段类型。 
所有后续值必须具有相同的数据类型，或者必须可以将后续值强制转换为相同的数据类型。

数组不支持混合类型:  [ 10, "some string" ]

数组可以包含空值，这些值可以由配置的null_value替换，也可以完全跳过。 
空数组[]被视为缺少字段 - 没有值的字段。

没有任何东西需要预先配置才能在文档中使用数组，它们是开箱即用的：
```
PUT my_index/_doc/1
{
  "message": "some arrays in this document...",
  "tags":  [ "elasticsearch", "wow" ], 
  "lists": [ 
    {
      "name": "prog_list",
      "description": "programming list"
    },
    {
      "name": "cool_list",
      "description": "cool stuff list"
    }
  ]
}

PUT my_index/_doc/2 
{
  "message": "no arrays in this document...",
  "tags":  "elasticsearch",
  "lists": {
    "name": "prog_list",
    "description": "programming list"
  }
}

GET my_index/_search
{
  "query": {
    "match": {
      "tags": "elasticsearch" 
    }
  }
}
```

## Multi-value fields and the inverted index

所有字段类型都支持开箱即用的多值字段这一事实是Lucene原有的特性。 
Lucene旨在成为一个全文搜索引擎。 为了能够在一大块文本中搜索单个单词，Lucene将文本标记为单个术语，并将每个术语分别添加到**反向索引**中。

这意味着即使是简单的文本字段也必须能够默认支持多个值。 
当添加其他数据类型（例如数字和日期）时，它们使用与字符串相同的数据结构，因此可以免费获得多值。

---

[Nested datatype]: https://www.elastic.co/guide/en/elasticsearch/reference/current/nested.html