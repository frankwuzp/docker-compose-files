# Uptime Kume

## 简介

Uptime Kuma 是一个类似于“Uptime Robot”的自托管监控工具。

[Uptime Kuma Demo](https://demo.uptime.kuma.pet:27000/)

## 目录结构规划

```
usr
└─ local
   └─ bin
      └─ docker
         └─ uptime-kuma
            ├── docker-compose.yml
            └── data
```

[_docker-compose.yml_](docker-compose.yml)
```
version: "3"
services:
  uptime-kuma:
    image: louislam/uptime-kuma
    container_name: uptime-kuma
    network_mode: "bridge"
    volumes:
      - ./data:/app/data
    ports:
      - 49301:3001
    restart: always
    deploy:
        resources:
            limits:
              cpus: '0.05'
              memory: 88M 
            reservations:
              cpus: '0.0050'
              memory: 40M 
```
_注：_

_deploy 参数后加入了内存限制，表示一般情况下容器的 cpus 使用被限制在 5%, 内存为 88 MB ，当服务器资源紧张时，cpus 占用变为 0.50%, 内存占用被限制为 40 MB_

_因未加入交换内存限制，部署时加上 `--compatibility` 兼容性参数，类似于 `docker-compose --compatibility up -d`_



## 使用

### 部署服务

在对应的文件夹下使用 `docker-compose up -d`

预期结果：

```bash
$ docker-compose up -d
Creating aliyundriver ... done
```



### 查看容器状态

使用命令 `docker ps` 查看容器状态

预期结果：

```bash
$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED             STATUS             PORTS                                                           NAMES
0d6a05ae9bdd   zx5253/webdav-aliyundriver           "sh -c 'java $JAVA_O…"   17 seconds ago   Up 16 seconds         0.0.0.0:49188->8080/tcp, :::49188->8080/tcp                     aliyundriver
```

### 停止删除容器

在对应的文件夹下使用 `docker-compose down`

预期结果：

```bash
$ docker-compose down
Stopping aliyundriver ... done
Removing aliyundriver ... done
``` 

## Ref

- [louislam/uptime-kuma](https://github.com/louislam/uptime-kuma)
- [louislam/uptime-kuma - Docker Hub](https://hub.docker.com/r/louislam/uptime-kuma)