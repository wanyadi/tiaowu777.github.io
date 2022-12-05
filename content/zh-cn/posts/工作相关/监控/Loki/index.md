---
title: "Loki"
date: 2022-11-18T10:24:01+09:00
hero: Monitor.png
#author:
#  name: James Ray
#  image: images/author/james.jpg
draft: true
menu:
  sidebar:
    name: Loki监控
    identifier: Loki监控
    parent: 监控
    weight: 220
---
一、简介
----

今天我们要介绍的是漫威旗下的 Loki。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a68b9d606c9649fc92a4ef6ae3b22c00~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

不好意思，串戏了，是 Grafana 旗下的 Loki。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d25d0857f9f2468aa7b970229334729c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

Loki 是受 Prometheus 启发可水平可扩展，高可用的多租户日志聚合系统。 它被设计得非常轻量高效且易于操作。 它不索引日志的内容，而是为每个日志流设置一组标签。

二、Loki / Promtail / Grafana vs EFK
----------------------------------

EFK（Elasticsearch，Fluentd，Kibana）技术栈用于提取，可视化和查询来自各种来源的日志。

Elasticsearch 中的数据作为非结构化 JSON 对象存储在磁盘上。每个对象的键和每个键的内容都被索引。然后可以使用 JSON 对象查询数据以定义查询（称为查询 DSL），也可以通过 Lucene 查询语言查询数据。

相比之下，Loki 在单二进制模式下可以将数据存储在磁盘上，在水平可伸缩模式下，数据可存储在诸如 S3，GCS 或 Cassandra 之类的云存储系统中。日志以纯文本格式存储，并带有一组标签名称和值，其中仅对标签对进行索引。这种设置使得它比全索引更加节省空间，并且允许开发人员从其应用程序积极地进行日志记录。使用 LogQL 查询 Loki 中的日志。但是，由于这种设计权衡，基于内容（即日志行中的文本）进行过滤的 LogQL 查询需要在搜索窗口中加载与查询中定义的标签匹配的所有块。

Fluentd 通常用于收集日志并将其转发到 Elasticsearch。 Fluentd 被称为数据收集器，它可以从许多来源提取日志，对其进行处理，然后将其转发到一个或多个目标。

相比之下，Promtail 则是专门针对 Loki 量身定制的。它用来发现存储在磁盘上的日志文件，并将它们与一组标签关联起来转发给 Loki。 Promtail 可以为与 Promtail 在同一节点上运行的 Kubernetes Pod 进行服务发现，充当容器 sidecar 或 Docker 日志记录驱动程序，从指定的文件夹中读取日志，并跟踪 systemd 日志。

Loki 用一组标签标识日志的方式类似于 Prometheus 表示度量的方式。当在 Prometheus 旁边的环境中部署时，由于使用相同的服务发现机制，来自 Promtail 的日志通常具有与应用程序指标相同的标签。具有相同级别的日志和指标使用户能够在指标和日志之间无缝地进行上下文切换，从而有助于根本原因分析。

Kibana 用于可视化和搜索 Elasticsearch 数据，并且在对该数据进行分析时非常强大。 Kibana 提供了许多可视化工具来进行数据分析，例如位置图，用于异常检测的机器学习以及用于发现数据关系的图形。可以将警报配置为在发生意外情况时通知用户。

相比之下，Grafana 专门针对来自 Prometheus 和 Loki 等来源的时间序列数据量身定制。可以设置仪表板以可视化指标，并且可以使用 explore 视图对数据进行特殊查询。与 Kibana 一样，Grafana 也支持根据您的指标进行告警。

三、告警
----

我们可以为 Loki 日志数据创建和管理 Prometheus 风格的警报规则，无需上报或者修改配置文件或安装其他监控工具。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f76a5bf147b4fd896c6fb982a27a64b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

四、关联指标和日志
---------

Loki 日志和 Prometheus 指标等都可以展示到 Grafana 中，通过使用与 Prometheus 完全相同的服务发现，Loki 可以保证您的日志具有和您的指标一致的标签。这样我们可以在指标和日志之间无缝切换，节省时间。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ff11f8a29ef4d18a36bc8cc604e16b8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

五、LogQL
-------

LogQL 受 PromQL 的启发，它就像一个分布式的 grep 日志聚合查看器。和 PromeQL 一样，LogQL 也是使用标签和运算符进行过滤，它主要分为两个部分：

*   log stream selector （日志流选择器）
*   filter expression （过滤器表达式）

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8e88e63f67be4360aa90de95fb6c9b4b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

另外还可以定义 Functions 和 Template functions，更多请查看官方文档：[grafana.com/docs/loki/l…](https://grafana.com/docs/loki/latest/logql/?fileGuid=zdkyB8RLyEFQ8xA6)

六、强大的 Grafana 生态
----------------

我们可以在 Grafana 中展示 Loki 收集的日志，绘制出各种图表。

图示： nginx 日志的 dashboards。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d4190ffbd9414c4db987e79b67b07295~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9a9cfda8ef4c46cf963e46584c3a7a73~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

更多请查看：[grafana.com/grafana/das…](https://grafana.com/grafana/dashboards/12559?fileGuid=zdkyB8RLyEFQ8xA6)

七、官方视频介绍
--------

*   [演示：Grafana 和可观察性入门](https://grafana.com/go/webinar/march-live-demo-days?pg=oss-loki&plcmt=hero-txt&fileGuid=zdkyB8RLyEFQ8xA6)
*   [Loki 入门](https://grafana.com/go/webinar/intro-to-loki-like-prometheus-but-for-logs/?pg=oss-loki&plcmt=hero-txt&fileGuid=zdkyB8RLyEFQ8xA6)
*   [Loki 基本配置设置](https://grafana.com/go/webinar/logging-with-loki-essential-configuration-settings/?pg=oss-loki&plcmt=hero-txt&fileGuid=zdkyB8RLyEFQ8xA6)
*   [Loki 2.0 的可观察性](https://grafana.com/go/webinar/observability-with-loki-2.0/?pg=oss-loki&plcmt=hero-txt&fileGuid=zdkyB8RLyEFQ8xA6)
*   [Grafana 企业日志演示](https://grafana.com/go/webinar/grafana-enterprise-logs-logging-with-security-and-scale/?pg=oss-loki&plcmt=hero-txt&fileGuid=zdkyB8RLyEFQ8xA6)

八、总结
----

近实时查询、支持 REST Api、低资源占用和对 k8s 更加友好，这样的日志系统你要不要试试呢？

号外（mica-logging 也添加 loki 支持）：[github.com/lets-mica/m…](https://github.com/lets-mica/mica/issues/36)

我是如梦技术（春哥）咱们下次再见！