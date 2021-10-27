# webdav-aliyundriver

## 简介

GitHub 上开源的阿里云盘的webdav协议，只需要简单配置，就可让阿里云盘变身为webdav协议的文件服务器。 

## 目录结构规划

```
usr
└─ local
   └─ bin
      └─ aliyundriver
         ├── docker-compose.yml
         └── refresh-token
```

[_docker-compose.yml_](docker-compose.yml)
```
version: "3.0"
services:
  webdav-aliyundriver:
    image: zx5253/webdav-aliyundriver
    container_name: aliyundriver
    network_mode: "bridge"
    environment:
      - TZ=Asia/Shanghai
      - ALIYUNDRIVE_REFRESH_TOKEN=refreshToken
      - ALIYUNDRIVE_AUTH_USER-NAME=admin
      - ALIYUNDRIVE_AUTH_PASSWORD=admin_password
      - JAVA_OPTS=-Xmx512m
    volumes:
      - /usr/local/bin/aliyundriver/:/etc/aliyun-driver/
    ports:
      - 49188:8080
    restart: always
```
_注：_

_环境变量`JAVA_OPTS=-Xmx512m`表示最大内存限制为512m_

## 浏览器获取 refresh_token 的方式

1. 浏览器打开阿里云盘官网并登录：https://www.aliyundrive.com/drive/
2. 按F12打开开发者工具，依次点击 Application -> Local Storage -> `https://www.aliyundrive.com/` , 选择右边的 `token`, 此时可以看到里面的数据，找到 `refresh_token` 的值，复制出来即可。（格式为小写字母和数字，不要复制双引号。例子：ca6bf2175d73as2188efg81f87e55f11）

## 使用

### 部署服务

在对应的文件夹下使用 `docker-compose up -d`

预期结果：

```bash
$ docker-compose up -d
Creating aliyundriver ... done
```

### 登录查看

浏览器输入链接 `http://localhost:端口号/admin` 登录阿里云盘界面，其中 `端口号` 如设定的是 `80` 则可省去。


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

- [zxbu/webdav-aliyundriver](https://github.com/zxbu/webdav-aliyundriver)
- [zx5253/webdav-aliyundriver - Docker Image](https://hub.docker.com/r/zx5253/webdav-aliyundriver)