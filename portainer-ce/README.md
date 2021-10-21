# portainer-ce

## 简介

Portainer 是一个轻量级的管理 UI，可轻松管理 Docker 和 Kubernetes 集群。

## 目录结构规划

```
docker
└─ portainer
  ├─ docker-compose.yml
  └─ portainer_data
```

[_docker-compose.yml_](docker-compose.yml)
```
version: "3.3"

services:
  portainer:
    container_name: portainer
    image: portainer/portainer-ce
    restart: unless-stopped
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./portainer_data:/data
    deploy:
        resources:
            limits:
              cpus: '0.05'
              memory: 30M 
            reservations:
              cpus: '0.0005'
              memory: 10M 

volumes:
  portainer_data:
```

_注：deploy 参数后加入了内存限制，表示一般情况下容器的 cpus 使用被限制在 5%, 内存为 30 MB ，当服务器资源紧张时，cpus 占用变为 0.05%, 内存占用被限制为 10 MB_
_因未加入交换内存限制，部署时加上 `--compatibility` 兼容性参数，类似于 `docker-compose --compatibility up -d`_

## 使用

### 部署服务

在对应的文件夹下使用 `docker-compose up -d`

预期结果：

```bash
$ docker-compose up -d
[+] Running 2/2
 ⠿ Network portainer_default  Created                                       
 ⠿ Container portainer        Started
```

### 查看容器状态

使用命令 `docker ps` 查看容器状态

预期结果：

```bash
$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED             STATUS             PORTS                                                           NAMES
6fa5f4d98cec   portainer/portainer-ce   "/portainer"             3 minutes ago       Up 3 minutes       8000/tcp, 9443/tcp, 0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   portainer
```

### 停止删除容器

在对应的文件夹下使用 `docker-compose down`

预期结果：

```bash
$ docker-compose down
[+] Running 2/2
 ⠿ Container portainer        Removed                                       
 ⠿ Network portainer_default  Removed 
``` 