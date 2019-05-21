# Text datatype

用于索引全文值的字段，例如电子邮件正文或产品说明。 
分析这些字段，即它们通过**分析器**传递，以在索引之前将字符串转换为单个术语的列表。 
分析过程允许Elasticsearch在每个全文字段中搜索单个单词。 
文本字段不用于排序，很少用于聚合（尽管重要的文本聚合是一个值得注意的例外）。

如果您需要索引电子邮件地址，主机名，状态代码或标记等结构化内容，则可能应该使用关键字字段。

有时同时拥有相同字段的全文（文本）和关键字（关键字）版本是有用的：一个用于全文搜索，另一个用于聚合和排序。 
这可以通过[multi-fields][]实现。


---
[multi-fields]: https://www.elastic.co/guide/en/elasticsearch/reference/current/multi-fields.html