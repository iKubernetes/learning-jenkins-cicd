# 基于Docker的Jenkins分布式构建环境

本示例仓库用于为Jenkins Controller提供基于Docker容器的静态Agent。

### 基于docker容器的 jnlp Agent

使用方法：

- 首先，在Jenkins上，开启允许jnlp agent接入：系统管理 --> 全局安全配置

- 而后，在Jenkins Controller上创建两个固定的Agent，其标识分别为agent01.magedu.com和agent02.magedu.com，启动方式为“通过Java Web启动代理”；

- 接着，在Jenkins Controller上获取这两个Agent的Secret，并更新到docker-compose-inbound-agent.yml文件中对应的agent之上；

- 最后，运行如下命令，启用docker jnlp agent

  ```bash
  docker-compose -f docker-compose-inbound-agent.yml up -d
  ```

### 基于docker容器的 ssh agent

使用方法：

- 首先，使用ssh-keygen命令生成一对rsa格式的密钥；

- 而后，将私钥创建为Jenkins Controller之上的"SSH Username with private key"类型的Credential，用户名为“jenkins”，私钥即为前一步中由命令生成的私钥；

- 接着，将ssh-keygen命令生成的公钥信息更新到docker-compose-ssh-agent.yml文件中的各agent上的环境变量JENKINS_AGENT_SSH_PUBKEY的值；两个agent使用同一个公钥即可； 

- 再接着，在Jenkins Controller上添加两个固定的Agent，启动方式为“Lanch aget via SSH”，各agent的标识分别为ssh-agent01.magedu.com和ssh-agent02.magedu.com；

- 最后，运行如下命令，启用docker jnlp agent

  ```bash
  docker-compose -f docker-compose-ssh-agent.yml up -d
  ```

  ### 说明

  基于Docker容器的静态Agent，其基础系统环境由相应的Docker Image生成，缺少很多工具程序。需要用到镜像中不具有的程序时，可以通过在Jenkins上设置全局工具程序，让Agent自动安装，也可自行基于基础镜像构建带有必要工具的镜像来。



