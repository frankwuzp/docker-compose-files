# mysql

## 简介

MySQL 是世界上最流行的开源数据库。凭借其久经考验的性能、可靠性和易用性，MySQL 已成为基于 Web 的应用程序的领先数据库选择，涵盖从个人项目和网站，到电子商务和信息服务，一直到备受瞩目的整个范围网络资产，包括 Facebook、Twitter、YouTube、Yahoo! 等等。

## 目录结构规划

```
docker
└─ mysql
  ├─ docker-compose.yml
  ├─ conf
  │  └─ my.cnf
  └─ db
```

[_docker-compose.yml_](docker-compose.yml)
```
version: '3'
services:
  db:
    image: 'mysql'
    restart: always
    container_name: 'mysql'
    environment:
      MYSQL_ROOT_PASSWORD: 'your_password'
      MYSQL_DATABASE: 'datebase_name'
      MYSQL_USER: 'user_name'
      MYSQL_PASSWORD: 'your_password'
    ports:
      - '3306:3306'
    volumes:
      - './db:/var/lib/mysql'
      - './conf/my.cnf:/etc/my.cnf''
    deploy:
        resources:
            limits:
              cpus: '0.10'
              memory: 800M
            reservations:
              cpus: '0.005'
              memory: 450M
```
_注：_

_deploy 参数后加入了内存限制，表示一般情况下容器的 cpus 使用被限制在 10%, 内存为 800 MB ，当服务器资源紧张时，cpus 占用变为 0.5%, 内存占用被限制为 450 MB_

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

## Ref

- [Mysql - Official Image | Docker Hub](https://hub.docker.com/_/mysql)