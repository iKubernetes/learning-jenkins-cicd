# 基于Docker的Jenkins分布式构建环境

该示例会运行三个服务：
- jenkins01：主机名为master01.magedu.com，别名为master01和jenkins_master01，IP地址为172.31.8.2；
  - 容器与端口8080与主机端口8080绑定；
  - 容器与端口50000与主机端口50000绑定；

- jenkins02：主机名为slave01.magedu.com，别名为slave01和jenkins_slave01，IP地址为172.31.8.11；
  - 容器与端口8080与主机端口8081绑定；

- jenkins03：主机名为slave02.magedu.com，别名为slave02和jenkins_slave02，IP地址为172.31.8.12；
  - 容器与端口8080与主机端口8082绑定；


## 环境初始化过程

### 第一步：配置master主机

在master上添加slave主机。



### 第二步：启动各Agent



