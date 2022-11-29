# GitLab CE

该项目中使用了GitLab官方制作的gitlab-ce镜像。

### Init Password

获取 root init password

```bash
docker-compose exec gitlab grep 'Password:' /etc/gitlab/initial_root_password
```

