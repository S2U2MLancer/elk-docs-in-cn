# Monitoring Elasticsearch

Elastic监控功能使您可以轻松监控Elasticsearch集群的运行状况。 监控指标从每个节点收集并存储在Elasticsearch索引中。

在生产环境中，建议**将监视数据存储在单独的监视集群中**。 请参阅[Monitoring in a production environment][]。

根据其持久性UUID，每个Elasticsearch节点都被认为是唯一的，该UUID在一开始会被写入[path.data][]目录，该目录默认为**./data**。

Elasticsearch中的监视关联的所有设置必须在每个节点的elasticsearch.yml文件中设置，或者在动态集群的配置中设置。
有关更多信息，请参阅[Configuring monitoring][]。

---
[Monitoring in a production environment]: https://www.elastic.co/guide/en/elastic-stack-overview/6.6/monitoring-production.html
[path.data]: https://www.elastic.co/guide/en/elasticsearch/reference/current/path-settings.html
[Configuring monitoring]: https://www.elastic.co/guide/en/elasticsearch/reference/current/configuring-monitoring.html