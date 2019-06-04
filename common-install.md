# Docker

- [安装 Docker](https://docs.docker.com/install/linux/docker-ce/centos/)
- [非 Root 运行 Docker](https://askubuntu.com/questions/477551/how-can-i-use-docker-without-sudo)
- 修改进程配置，远程连接，修改 /usr/lib/systemd/system/docker.service 下 docker.service 配置

```
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock --containerd=/run/containerd/containerd.sock
```

# MySQL

- 修改密码 MYSQL_ROOT_PASSWORD、bind-address

```
docker run -d --name enfi-cpool-mysql --net host -e MYSQL_ROOT_PASSWORD=PZCvuBs8n8%wsxR9 -v /home/piers/mysql/conf:/etc/mysql/conf.d -v /home/piers/mysql/data:/var/lib/mysql mysql
```

``` mysql.conf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html
[mysqld]
bind-address=0.0.0.0
```

# Redis

- 复制配置文件，修改密码、bind 等

```
docker run --net host -v /home/piers/redis/redis.conf:/usr/local/etc/redis/redis.conf -d --name enfi-cpool-redis redis redis-server /usr/local/etc/redis/redis.conf
```

# Nginx

```
docker run -d --name enfi-cpool-nginx -v /home/piers/nginx/conf:/etc/nginx:ro -v /home/piers/nginx/logs:/logs --net host nginx```