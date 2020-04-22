# Match Query

`match`查询接受**text/numerics/dates**类型的字段, 解析它们的值并构造相关的查询.
例如:

```http
GET /_search
{
    "query": {
        "match" : {
            "message" : "this is a test"
        }
    }
}
```

## match

`match`查询是`boolean`查询的一种. 通过对提供的文本内容进行解析, 并构造出一个boolean查询.
`operator`参数可以为or(默认)或者and, 用于boolean查询条件的组合方式.
可以通过`minimum_should_match`参数来设置用于匹配的最小数量的should条件.

可以通过`analyzer`参数来配置指定的解析器来对文本内容进行解析.
默认使用mapping中字段显示指定的解析器, 或者默认的搜索解析器.

可以将lenient参数设置为true以忽略由数据类型不匹配引起的异常，
例如尝试使用文本查询字符串查询数字字段。 默认为false。

## Fuzziness

`fuzziness`允许基于被查询字段的类型的模糊匹配. 详见[Fuzziness][].

TODO


---
[Fuzziness]: https://www.elastic.co/guide/en/elasticsearch/reference/current/common-options.html#fuzziness