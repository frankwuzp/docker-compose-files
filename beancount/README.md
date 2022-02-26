# beancount

## 简介

[beancount](https://github.com/beancount/beancount#description) 是一款强大的复式记账语言，可通过文本记录金融交易，并提供功能强大的 Web 界面和各种报告。

更多功能和用法参见源仓库👉[Beancount Documentation](https://beancount.github.io/docs/)

## 目录结构规划

```
usr
└─ local
   └─ docker
      └─ beancount
         ├─ docker-compose.yml
         ├─ bak
         ├─ config
         ├─ data
         └─ icons

```

```bash
$ docker network create beancount
$ sudo vi docker-compose.yml
```

[_docker-compose.yml_](docker-compose.yml)
```
version: "3.9"
services:
  app:
    container_name: beancount-gs
    image: xdbin/beancount-gs:latest
    network_mode: "beancount"
    restart: unless-stopped
    ports:
      - "10000:80"
    # 启动 beancount 服务
    # volumes 挂载目录会导 /app/public/icons 中的图标被覆盖，这里将默认图标在挂载后重新拷贝图标
    command: >
      sh -c "cp -rn /app/public/default_icons/* /app/public/icons && ./beancount-gs -p 80"
    # 如不需要默认图标，可使用这句命令
#    command: >
#      sh -c "./beancount-gs -p 80"
    volumes:
      - "./data:/data/beancount"
      - "./icons:/app/public/icons"
      - "./config:/app/config"
      - "./bak:/data/beancount/<$进入 bash 后查看目录名称>/bak"
    deploy:
        resources:
            limits:
              cpus: '0.05'
              memory: 66M
            reservations:
              cpus: '0.0015'
              memory: 40M
```
_注：_

_deploy 参数后加入了内存和 cpus 限制，部分服务器不支持 CPU 限制，会被自动忽略_

## 使用

### 部署服务

在对应的文件夹下使用 `docker-compose up -d`

预期结果：

```bash
$ docker-compose up -d
Creating beancount-gs ... done
```

### 如何使用

浏览器访问 `http://<ip地址>:10000` 按步骤创建个人账本，存储位置可填 `/data/beancount`

### 查看容器状态

使用命令 `docker ps` 查看容器状态

预期结果：

```bash
$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED             STATUS             PORTS                                                           NAMES
3301d44dc02a   xdbin/beancount-gs   "sh -c './beancount-…"   4 hours ago   Up 4 hours   0.0.0.0:10000->80/tcp, :::10000->80/tcp   beancount-gs
```

### 停止删除容器

在对应的文件夹下使用 `docker-compose down`

预期结果：

```bash
$ docker-compose down
Stopping beancount-gs  ... done
``` 

## Ref

- [beancount/beancount: Beancount: Double-Entry Accounting from Text Files.](https://github.com/beancount/beancount#description)
- [BaoXuebin/beancount-gs: 基于 beancount 提供个人财务管理的 RESTful API 服务（包含前端页面）](https://github.com/BaoXuebin/beancount-gs)