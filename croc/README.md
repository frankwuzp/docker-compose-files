# croc

## 简介

croc 是一种传输工具，可以使任何两台计算机简单安全地传输文件和文件夹。 在创作者看来，croc 是唯一可以执行以下所有操作的 CLI 文件传输工具：

- 允许任意两台计算机传输数据（使用中继）
- 提供端到端加密（使用 PAKE ）
- 实现轻松的跨平台传输 (Windows、Linux、Mac)
- 允许多个文件传输
- 允许恢复被中断的传输
- 不需要本地服务器或端口转发
- ipv6 优先和 ipv4 回退
- 可以使用代理，比如 tor

更多功能和用法参见源仓库👉[GitHub：schollz/croc](https://github.com/schollz/croc)

## 目录结构规划

```
usr
└─ local
   └─ docker
      └─ croc
         └── docker-compose.yml
```

```bash
$ docker network create croc
$ sudo vi docker-compose.yml
```

[_docker-compose.yml_](docker-compose.yml)
```
version: "3"

services:
  croc:
    image: schollz/croc
    container_name: croc
    network_mode: "croc"
    restart: always
    ports:
      - 9009-9013:9009-9013
    environment:
      - CROC_PASS=YOURPASSWORD
    deploy:
        resources:
            limits:
              cpus: '0.01'
              memory: 20M 
            reservations:
              cpus: '0.0005'
              memory: 10M 
```
_注：_

_deploy 参数后加入了内存和 cpus 限制，部分服务器不支持 CPU 限制，会被自动忽略_

## 使用

### 部署服务

在对应的文件夹下使用 `docker-compose up -d`

预期结果：

```bash
$ docker-compose up -d
Creating croc ... done
```

### 如何使用

在上传的客户端使用命令行：

```bash
croc --pass YOURPASSWORD --relay "yourdomain.com:9009" send <files or >
```

### 查看容器状态

使用命令 `docker ps` 查看容器状态

预期结果：

```bash
$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED             STATUS             PORTS                                                           NAMES
3301d44dc02a   schollz/croc             "/croc-entrypoint.sh…"   4 hours ago   Up 4 hours   0.0.0.0:9009-9013->9009-9013/tcp, :::9009-9013->9009-9013/tcp   croc
```

### 停止删除容器

在对应的文件夹下使用 `docker-compose down`

预期结果：

```bash
$ docker-compose down
Stopping croc  ... done
``` 
### 番外

**安装 croc** 

方法一：命令行
```bash
curl https://getcroc.schollz.com | bash
```

方法二：仓库下载 [the latest releases](https://github.com/schollz/croc/releases/latest)

**自定义短语**

```bash
croc send --code [code-phrase] [file(s)-or-folder]
```

## Ref

- [schollz/croc](https://github.com/schollz/croc)
- [schollz/croc | Docker Hub](https://hub.docker.com/r/schollz/croc)
- [croc - 小众软件](https://www.appinn.com/croc/)