# SonarQube

The following requirements and recommendations apply when running SonarQube in Docker in production.

### Set vm.max_map_count to at least 262144
The vm.max_map_count kernel setting must be set to at least 262144 for production use.

How you set vm.max_map_count depends on your platform.

#### Linux

To view the current value for the vm.max_map_count setting, run:

```bash
grep vm.max_map_count /etc/sysctl.conf
vm.max_map_count=262144
```

To apply the setting on a live system, run:

```bash
sysctl -w vm.max_map_count=262144
```

To permanently change the value for the vm.max_map_count setting, update the value in /etc/sysctl.conf.

### 启动SonarQube Server

运行docker-compose up命令，即可完成环境初始化和启动。
