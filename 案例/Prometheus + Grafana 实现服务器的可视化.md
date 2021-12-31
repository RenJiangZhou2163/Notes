# Prometheus + Grafana 实现服务器的可视化

解博 译 [分布式实验室](javascript:void(0);) *今天*

https://mp.weixin.qq.com/s/8wGnEOu-gWHgxKDSZS2MFg

![图片](Prometheus + Grafana 实现服务器的可视化.assets/640.webp)



## Prometheus 简介

Prometheus 是一个开源监控工具，实现了高维数据模型。Prometheus 有多种数据可视化模式，其中一种是集成 Grafana。Prometheus 以高效的自定义格式将时间序列数据存储在内存和本地磁盘上。

Prometheus 有许多客户端可用于轻松监控服务，也可以轻松创建自定义客户端。每台服务器的可靠性都是独立的，仅依赖本地存储。用 Golang 编程语言编写，所有二进制文件都是静态链接的，易于部署。

Prometheus 采用拉取策略而不是推送策略，即 Prometheus 以一定的时间间隔从 exporter 那里拉取数据，而不是 exporter推送数据到 Prometheus。这种方式有其自身的优点和缺点，但我们不讨论这些细节。



## Grafana 简介

Grafana 是一款开源可视化和分析软件，它允许你查询、可视化、提醒和探索您的指标，无论这些指标存储在哪里。Grafana 支持数十种数据库，我们可以创建一个仪表盘来可视化它们全部。

Grafana 还提供报警，直观地定义阀值，并通过 Slack、 PagerDuty 和其他平台获得通知。Grafana 还提供了多种选项来查看我们的数据，从热力图到直方图，从图形到地理地图。Grafana 有大量的可视化选项可以帮助我们更好地理解数据。我正在使用 Ubuntu 18.04，并将显示与其相关的整个配置。



## Prometheus 安装



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-16375656033782.webp)

```shell
wget https://github.com/prometheus/prometheus/releases/download/v2.21.0/prometheus-2.21.0.linux-amd64.tar.gz
tar -xzf prometheus-2.21.0.linux-amd64.tar.gz
cd prometheus-2.21.0.linux-amd64/
./prometheus
```

安装非常简单，执行这些命令将会让 Prometheus 服务器在端口 9090 中运行。Prometheus 在端口9090上的仪表板如下图所示：



<img src="Prometheus + Grafana 实现服务器的可视化.assets/640-16375656033783.webp" alt="图片" style="zoom:150%;" />



如前所述，从 Prometheus 中抓取的指标发生在恒定的时间段内，因此可以在路径 /metrics 中查看它们。

![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-16375656033784.webp)



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-16375656033785.webp)



这些指标用于形成具有各种聚合函数的复杂表达式，以我们想要的形式进行可视化，这在 promql 的帮助下基本上是可能的。Prometheus 中的图形可视化非常基本，没有提供太多自定义，因此我们将使用 Grafana。



## 节点 Exporter 安装



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-16375656033786.webp)

```shell
wget https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz
tar -xzf node_exporter-1.0.1.linux-amd64.tar.gz
cd node_exporter-1.0.1.linux-amd64/
./node_exporter
```

上述命令将安装节点 exporter 并在端口 9100 上运行，并且可以从 /metrics 扩展中抓取指标。



我创建了 3 个虚拟机并在所有虚拟机中安装了节点 exporter，以提供更好的可视化效果。所以在安装之后，我们必须告诉 Prometheus 从哪里抓取指标，这可以通过编辑 prometheus.yml 文件来完成。我们只需要在 scrape_configs 中添加一个新作业，指定目标中的 IP 地址和端口。在 prometheus.yml 文件中添加目标并重新启动 Prometheus 服务器后，我们可以在仪表板以及 /targets 路径中看到新目标及其状态。



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-16375656033787.webp)



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-16375656033798.webp)



确保所有目标都已启动，如果没有，请检查是否为该 VM 实例开放了 9100 端口。你还可以查看 Prometheus 从每个 exporter 抓取的时间以及上次抓取的时间。



## Grafana 安装



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-16375656033799.webp)

```shell
wget https://dl.grafana.com/oss/release/grafana-7.1.5.linux-amd64.tar.gz
tar -xzf grafana-7.1.5.linux-amd64.tar.gz
cd grafana-7.1.5.linux-amd64/
./bin/grafana-server
```

通过运行上述命令即可完成安装，Grafana 运行在端口 3000。默认的用户名和密码均为“admin”。



当我们进入仪表板，我们需要添加一个数据源，在我们的例子中是 Prometheus。我们只需要提供 Prometheus URL 并点击保存和测试按钮。如果我们看到一个成功的提示框，说数据源正在工作，那么我们就可以开始了。

<img src="Prometheus + Grafana 实现服务器的可视化.assets/640-163756560337910.webp" alt="图片" style="zoom:150%;" />



我们可以使用自定义的查询语句创建自己的仪表板和面板，但这是一项乏味的工作。因此，为了简化我们的工作，其他用户已经创建了一些仪表板，我们可以使用相同的仪表板并根据我们的需要调整表达式。我使用的是 1860 和 405，这些是我们导入仪表板的唯一ID。



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-163756560337911.webp)



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-163756560337912.webp)



导入完成后，我们会看到基于其表达式和时间范围的图表。你可以通过将时间范围减少到 5 分钟来深入了解。



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-163756560337913.webp)



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-163756560338014.webp)



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-163756560338015.webp)



Grafana 还允许我们查看合并多个 exporter的表单，以便更好地进行比较。



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-163756560338016.webp)



![图片](Prometheus + Grafana 实现服务器的可视化.assets/640-163756560338017.webp)



所以在上面的图片中，我们可以看到所有三个节点 exporter 的数据都被可视化了。

以上总结了 Prometheus 和 Grafana 的基本设置，用来可视化节点指标数据。如果你觉得有帮助，请点赞分享。

原文链接：https://medium.com/javarevisited/prometheus-grafana-setup-to-visualize-your-servers-924773b83f3f