# Tomcat with Manager app
用于为Jenkins Pipeline提供运行servlet应用的tomcat服务，支持从Manager部署应用。

- 容器监控的端口8080/TCP，映射的主机端口：8088/TCP；
- Manager的访问URL为：http://HOST_IP:8088/manager
  - 用户名：tomcat，密码：magedu.com
- 部署应用的默认路径为/usr/local/tomcat/webapps
