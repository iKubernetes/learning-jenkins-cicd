# Argo Rollouts Examples.

Rollouts资源配置示例。

### 01. 基础配置示例

配置文件：01-basic-rollouts-demo.yaml

```bash
kubectl apply -f 01-basic-rollouts-demo.yaml
```

#### 测试请求

向Ingress资源中指定的主机名hello.magedu.com发起测试请求...
```bash
while true; do curl http://hello.magedu.com; echo; sleep 0.$[$RANDOM%10]; done
```

#### rollout

```bash
kubectl argo rollouts set image rollouts-spring-boot-helloworld spring-boot-helloworld=ikubernetes/spring-boot-helloworld:v0.9.3
```

#### watch

```bash
kubectl argo rollouts get rollout rollouts-spring-boot-helloworld --watch
```

#### Promote

```bash
kubectl argo rollouts promote rollouts-spring-boot-helloworld
```

### 02.基于Ingress Nginx的Canary发布和流量迁移

配置文件：02-rollouts-with-ingress-nginx-traffic-shifting.yaml

**依赖项**: Ingress Nginx

```bash
kubectl apply -f 02-rollouts-with-ingress-nginx-traffic-shifting.yaml
```

#### Client
向Ingress资源中指定的主机名hello.magedu.com发起测试请求...
```bash
while true; do curl http://hello.magedu.com; echo; sleep 0.$[$RANDOM%10]; done
```

#### Rollout

```bash
kubectl argo rollouts set image rollouts-helloworld-with-traffic-shifting spring-boot-helloworld=ikubernetes/spring-boot-helloworld:v0.9.3
```

### 03.基于Prometheus和Ingress Nginx的渐进式交付

配置文件：03-rollouts-with-prometheus-analysis.yaml

**依赖项**: Ingress Nginx

```bash
kubectl apply -f 03-rollouts-with-prometheus-analysis.yaml
```
#### client

向Ingress资源中指定的主机名hello.magedu.com发起测试请求...

```bash
while true; do curl http://hello.magedu.com; echo; sleep 0.$[$RANDOM%10]; done
```

#### Prometheus Metrics

```
irate of requests that the response code not 5xx: sum(irate(istio_requests_total{reporter="source",destination_service="spring-boot-helloworld.default.svc.cluster.local",response_code!~"5.*"}[1m]))
```

```
irate of all requests: irate(istio_requests_total{reporter="source",destination_service="spring-boot-helloworld.default.svc.cluster.local"}[1m])
```

Successful Rate:

```
sum(irate(istio_requests_total{reporter="source",destination_service="spring-boot-helloworld.default.svc.cluster.local",response_code!~"5.*"}[1m]))/sum(irate(istio_requests_total{reporter="source",destination_service="spring-boot-helloworld.default.svc.cluster.local"}[1m]))
```


#### Rollout

```bash
kubectl argo rollouts set image rollouts-helloworld-with-analysis spring-boot-helloworld=ikubernetes/spring-boot-helloworld:v0.9.6 -n demo 
```

### Blue Green Rollout

#### Deploy

```bash
kubectl apply -f 05-argo-rollouts-bluegreen-demo.yaml -n demo
```

#### client

```bash
kubectl run client-$RANDOM --image ikubernetes/admin-box:v1.2 --rm -it --restart=Never --command -- /bin/bash
```

send requests...
```bash
while true; do curl http://spring-boot-helloworld.demo.svc.cluster.local; echo; sleep 1; done
```

#### Rollout

```bash
kubectl argo rollouts set image rollout-helloworld-bluegreen spring-boot-helloworld=ikubernetes/spring-boot-helloworld:v0.9.6 -n demo
```

#### Promote

```bash
kubectl argo rollouts promote rollout-helloworld-bluegreen -n demo
```

### Blue Green Rollout with Analysis

#### Deploy

```bash
kubectl apply -f 06-argo-rollouts-bluegreen-with-analysis.yaml -n demo
```

#### client

```bash
kubectl run client-$RANDOM --image ikubernetes/admin-box:v1.2 --rm -it --restart=Never --command -- /bin/bash
```

send requests...
```bash
while true; do curl http://spring-boot-helloworld.demo.svc.cluster.local; echo; sleep 1; done
```

#### Rollout

```bash
kubectl argo rollouts set image rollout-helloworld-bluegreen-with-analysis spring-boot-helloworld=ikubernetes/spring-boot-helloworld:v0.9.6 -n demo
```
