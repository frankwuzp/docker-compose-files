# phpadmin

## 简介

phpMyAdmin 是众多 MySQL 图形化管理工具中使用最为广泛的一种，是一款使用 PHP 编写的免费软件工具，旨在通过 Web 图形化操作界面对 MySQL 和 MariaDB 进行各种操作，如创建数据库，数据表和生成 MySQL 数据库脚本文件等。

## 目录结构规划

```
docker
└─ phpmyadmin
  ├─ docker-compose.yml
  └─ config
```

[_docker-compose.yaml_](docker-compose.yaml)
```
version: "3"
services:
  phpmyadmin:
    image: phpmyadmin
    container_name: phpmyadmin
    environment:
      - PMA_ARBITRARY=1 #optional
     # - PMA_ABSOLUTE_URI=https://phpmyadmin.example.com #optional
    volumes:
      - ./config:/config
    ports:
      - 8080:80
    restart: unless-stopped
    deploy:
        resources:
            limits:
              cpus: '0.05'
              memory: 100M
            reservations:
              cpus: '0.0005'
              memory: 50M
```
_注：deploy 参数后加入了内存限制，表示一般情况下容器的 cpus 使用被限制在 5%, 内存为 100 MB ，当服务器资源紧张时，cpus 占用变为 0.05%, 内存占用被限制为 50 MB_
_因未加入交换内存限制，部署时加上 `--compatibility` 兼容性参数，类似于 `docker-compose --compatibility up -d`_

## 使用

### 部署服务

在对应的文件夹下使用 `docker-compose up -d`

预期结果：

```bash
docker-compose up -d
[+] Running 2/2
 ⠿ Network phpmyadmin_default  Created
 ⠿ Container phpmyadmin        Started
```

### 查看容器状态

使用命令 `docker ps` 查看容器状态

预期结果：

```bash
$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED             STATUS             PORTS                                                           NAMES
a0409cc523db   phpmyadmin               "/docker-entrypoint.…"   3 minutes ago   Up 3 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp                           phpmyadmin
```

### 停止删除容器

在对应的文件夹下使用 `docker-compose down`

预期结果：

```bash
$ docker-compose down
[+] Running 2/2
 ⠿ Container phpmyadmin        Removed
 ⠿ Network phpmyadmin_default  Removed
``` 

## Ref

- [Phpmyadmin - Official Image | Docker Hub](https://hub.docker.com/_/phpmyadmin)
- [linuxserver/phpmyadmin - Docker Image | Docker Hub](https://hub.docker.com/r/linuxserver/phpmyadmin)
- [phpMyAdmin使用教程_叶落无痕的博客-CSDN](https://blog.csdn.net/u012767761/article/details/78238487)