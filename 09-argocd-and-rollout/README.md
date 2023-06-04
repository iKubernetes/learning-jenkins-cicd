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
irate(nginx_ingress_controller_requests{service=~"spring-boot-helloworld",namespace=~"default",status!~"[4-5].*"}[1m])
```

```
irate(nginx_ingress_controller_requests{service=~"spring-boot-helloworld",namespace=~"default"}[1m])
```

Successful Rate:

```
sum(irate(nginx_ingress_controller_requests{service=~"spring-boot-helloworld",namespace=~"default",status!~"[4-5].*"}[1m]))/sum(irate(nginx_ingress_controller_requests{service=~"spring-boot-helloworld",namespace=~"default"}[1m]))
```


#### Rollout

```bash
kubectl argo rollouts set image rollouts-helloworld-with-analysis spring-boot-helloworld=ikubernetes/spring-boot-helloworld:v0.9.3
```

### Blue Green Rollout

资源配置文件：04-rollouts-bluegreen-demo.yaml

#### Deploy

```bash
kubectl apply -f 04-rollouts-bluegreen-demo.yaml
```

#### client

send requests...
```bash
while true; do curl http://hello.magedu.com; echo; sleep 0.$[$RANDOM%10]; done
```

#### Rollout

```bash
kubectl argo rollouts set image rollout-helloworld-bluegreen spring-boot-helloworld=ikubernetes/spring-boot-helloworld:v0.9.3
```

#### Promote

```bash
kubectl argo rollouts promote rollout-helloworld-bluegreen
```

### Blue Green Rollout with Analysis

配置文件：05-rollouts-bluegreen-with-analysis.yaml

#### Deploy

```bash
kubectl apply -f 05-rollouts-bluegreen-with-analysis.yaml
```

#### client

send requests...
```bash
while true; do curl http://hello.magedu.com; echo; sleep 0.$[$RANDOM%10]; done
```

#### Rollout

```bash
kubectl argo rollouts set image rollout-helloworld-bluegreen-with-analysis spring-boot-helloworld=ikubernetes/spring-boot-helloworld:v0.9.3
```
