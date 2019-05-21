# Nested datatype

嵌套类型是对象数据类型的专用版本，它允许对象数组以可以彼此独立查询的方式进行索引。

## How arrays of objects are flattened

内部对象字段的数组不会以您期望的方式工作。 Lucene没有内部对象的概念，
因此Elasticsearch将对象层次结构扁平化为一个简单的字段名称和值列表。 例如，以下文件：
```
PUT my_index/_doc/1
{
  "group" : "fans",
  "user" : [ 
    {
      "first" : "John",
      "last" :  "Smith"
    },
    {
      "first" : "Alice",
      "last" :  "White"
    }
  ]
}
```
将被转换为
```
{
  "group" :        "fans",
  "user.first" : [ "alice", "john" ],
  "user.last" :  [ "smith", "white" ]
}
```
user.first和user.last字段被展平为多值字段，并且alice和white之间的关联将丢失。 
此文档将错误地匹配alice和smith的查询
```
GET my_index/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "user.first": "Alice" }},
        { "match": { "user.last":  "Smith" }}
      ]
    }
  }
}
```

## Using nested fields for arrays of objects

如果需要索引对象数组并保持数组中每个对象的独立性，则应使用嵌套数据类型而不是对象数据类型。 
在内部，**嵌套对象将数组中的每个对象索引为单独的隐藏文档**，这意味着可以使用**嵌套查询**独立于其他对象查询每个嵌套对象

---