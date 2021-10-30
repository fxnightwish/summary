# Redis安装部署

```shell
#如果没有安装gcc，先安装gcc
yum install gcc
#编译
make
#安装
make install
#启动,指定配置文件
redis-server /usr/local/redis/redis.conf
#查看是否可用
redis-cli ping
```

## 配置文件

```properties
daemon yes
port 6379
bind 0.0.0.0
requirepass 123456
```

## 集群配置

```properties
port 7000
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
```

## 创建集群

```shell
redis-cli --cluster create 192.168.182.136:6379 192.168.182.137:6379 192.168.182.138:6379 --cluster-replicas 2
```

