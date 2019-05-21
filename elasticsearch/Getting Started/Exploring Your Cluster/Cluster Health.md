# Cluster Health

让我们从基本运行状况检查开始，我们可以使用它来查看集群的运行情况。 
我们将使用curl来执行此操作，但您可以使用任何允许您进行HTTP / REST调用的工具。 
假设我们仍然在我们启动Elasticsearch的同一节点上打开另一个命令shell窗口。

要检查群集运行状况，我们将使用**_cat** API。 
您可以通过单击“查看控制台”或通过单击下面的“COPY AS CURL”链接并将其粘贴到终端中，在Kibana控制台中运行以下命令。
```
GET /_cat/health?v
```
并返回:
```
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1475247709 17:01:49  elasticsearch green           1         1      0   0    0    0        0             0                  -                100.0%
```

我们可以看到名为“elasticsearch”的群集处于绿色状态。

每当我们要求群集健康时，我们要么获得绿色，黄色或红色。

- 绿色 - 一切都很好（集群功能齐全）
- 黄色 - 所有数据都可用，但尚未分配一些副本（群集功能齐全）
- 红色 - 某些数据由于某种原因不可用（群集部分功能）

**NOTE**：当群集为红色时，它将继续提供来自可用分片的搜索请求，但您可能需要尽快修复它，因为存在未分配的分片。

同样从上面的响应中，我们可以看到总共1个节点，并且我们有0个分片，因为我们还没有数据。 
请注意，由于我们使用默认群集名称（elasticsearch），并且由于Elasticsearch**默认使用单播网络发现来查找同一台计算机上的其他节点**，
因此您可以在同一台计算机上启动多个节点并加入到同一个集群。 在这种情况下，您可能会在上面的响应中看到多个节点。

我们还可以获得群集中的节点列表，如下所示：
```
GET /_cat/nodes?v
```
并返回:
```
ip        heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
127.0.0.1           10           5   5    4.46                        mdi      *      PB2SGZY
```
在这里，我们可以看到一个名为“PB2SGZY”的节点，它是我们集群中当前的单个节点。