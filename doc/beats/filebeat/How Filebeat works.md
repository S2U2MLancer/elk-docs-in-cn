# How Filebeat works

在本主题中，您将了解Filebeat的关键构建块以及它们如何协同工作。
了解这些概念将帮助您针对特定场景的Filebeat配置作出的明智决策。

Filebeat由两个主要组件组成：输入(inputs)和收集器(harvesters)。
这些组件一起工作, 截取文件尾部的数据并将事件数据发送到您指定的输出(outputs)。

## What is a harvester?

收集器负责读取单个文件的内容。 收集器逐行读取每个文件，并将内容发送到输出。
为每个文件都会启动一个收集器。 收集器负责打开和关闭文件，这意味着文件描述符在收集器运行时保持打开状态。 
如果文件在被收集时被删除或重命名，Filebeat将继续读取该文件。 
这会产生副作用，即在收集器关闭之前，磁盘上的空间是保留的。 
默认情况下，Filebeat会保持文件处于打开状态，直到达到close_inactive。

关闭收集器会导致以下的结果:

- 文件句柄被关闭，如果在收集器仍在读取文件时删除了文件，则释放底层资源。
- 文件的收集只有在[scan_frequency][]执行过后才会再次开始.
- 如果在收集器关闭时移动或移除了文件，则不会继续收集文件。

要控制何时关闭收集器，使用[close_*][]相关的配置选项。

## What is an input?

输入负责管理收集器并查找要读取的所有源。

如果输入类型是*log*，则输入将查找机器上与定义的**glob**路径匹配的所有文件，并为每个文件启动收集器。
每个输入都在自己的Go例程中运行。

以下示例将Filebeat配置为从与指定的glob模式匹配的所有日志文件中获取行：
```
filebeat.inputs:
- type: log
  paths:
    - /var/log/*.log
    - /var/path2/*.log
```

Filebeat目前支持多种[输入类型][filebeat-input-types]。 每种输入类型都可以定义多次。
日志输入检查每个文件以查看是否需要启动收集器，以及是否已经运行，
或者是否可以忽略该文件（请参阅[ignore_older][filebeat-input-log-ignore-older]）。
收集器只会收集从上次关闭后到现在新增加的文件数据.

### How does Filebeat keep the state of files?

Filebeat保存每个文件的状态，并经常将状态刷新到注册表文件中的磁盘。
状态用于记住收集器正在读取的最后一行的偏移量以确保发送所有日志行。
如果无法访问输出（如Elasticsearch或Logstash），Filebeat会跟踪发送的最后一行，并在输出再次可用时继续读取文件。
在Filebeat运行时，状态信息也会保存在内存中以用于每个输入。 
重新启动Filebeat时，注册表文件中的数据用于重建状态，Filebeat会在最后一个已知位置继续运行每个收集器。

对于每个输入，Filebeat保持它找到的每个文件的状态。 由于可以重命名或移动文件，因此文件名和路径不足以标识文件。 
对于每个文件，Filebeat存储唯一标识符以检测先前是否收获了文件。

---
[scan_frequency]: https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-log.html#filebeat-input-log-scan-frequency
[close_*]: https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-log.html#filebeat-input-log-close-options
[filebeat-input-types]: https://www.elastic.co/guide/en/beats/filebeat/current/configuration-filebeat-options.html#filebeat-input-types
[filebeat-input-log-ignore-older]: https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-log.html#filebeat-input-log-ignore-older