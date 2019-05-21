# Range datatypes

以下类型支持范围查询:

- integer_range, [-2^31, 2^31 -1]
- float_range, 单精度32位IEEE754的浮点数
- long_range, [-2^63, 2^63 -1]
- double_range, 单双精度64位IEEE754的浮点数
- date_range, 时间使用64位无符号整数来表示从epoch时间开始的毫秒数
- ip_range, 支持IPv4/IPv6

## Parameters for range fields

范围类型可以接受的参数:

- coerce, 尝试将字符串转换为数字并截断整数的分数。 接受true（默认）和false。
- boost, 映射字段级查询时间提升。 接受浮点数，默认为1.0。
- index, 字段是否可悲搜索? true(默认)和false
- store, 字段是否可被存储以及通过_source字段来获取? true(默认)和false

---
