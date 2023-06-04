# 开启Ingress Nginx上的指标抓取

Ingress Nginx用于暴露指标的端口为10254，因此，我们需要在其服务上通过Prometheus专用的Annotations进行标记。这些功能默认并未开启。

```yaml

  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/port: "10254"
```



