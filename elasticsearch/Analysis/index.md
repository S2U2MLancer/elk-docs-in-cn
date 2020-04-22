# Analysis

将文本内容转换为多个标记(tokens)或者多个单词(terms), 并将他们插入到倒排索引树中, 这个过程称为解析.
通过解析器来执行解析, 解析器可以选择内置的解析器或者是针对索引自定义的解析器.

## Index time analysis

在执行索引时的解析过程, 内置的english解析器首先会将下面的文本转换成**不重复的标记**.
```
"The QUICK brown foxes jumped over the lazy dog!"
```
然后将每个**标记转换为小写**, 移除常见的定用词(例如the), 
并通过删除时态/单复数等**提取词干**部分(foxes → fox, jumped → jump, lazy → lazi),
最后将这些**词干加入到倒排索引**中.
```
[ quick, brown, fox, jump, over, lazi, dog ]
```

### Specifying an index time analyzer

TODO

## Search time analysis

在搜索索引时的解析过程, 相同的解析过程作用在全文搜索中的文本内容, 例如match查询.
它会将查询中的文本内容转换为和存储在倒排索引中相同格式的单词.

例如一个用户搜索`"a quick fox"`, 这个文本内容会被english解析器解析为`[ quick, fox ]`.

即使查询字符串中使用的确切单词没有出现在原始文本中(quick vs QUICK, fox vs foxes), 
因为我们用同一解析器应用于文本和查询内容, 查询内容中的单词与倒排索引中的单词完全匹配，
这意味着此查询将匹配我们的示例文档。

### Specifying a search time analyzer

TODO