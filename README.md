# docker-compose-files

## What's this

docker-compose 是一个定义和运行多容器 Docker 应用程序的工具。通过 docker-compose, 可以使用 YAML 文件来配置应用程序的服务。然后，只需使用一个命令，就可以从配置中创建并启动所有服务。

曾使用 docker 命令行部署服务，量多了顾不过来，需要更新时也麻烦，于是有了这一项目：使用 `docker-compose` 上线和更新容器，一键解决烦恼，省事多了😄
本仓库用于汇总常用的 `docker-compose.yml` 文件，不定期更新。

## How to use

1. 服务器安装 [docker-compose](https://github.com/docker/compose/releases)
2. 查看各项服务的目录规划，下载相应的 `docker-compose.yml`文件
3. 部署：`docker-compose up -d`
4. 停止和删除容器：`docker-compose down`

<font color=#D2691E>_注：把多个 `services:` 写入到同一个文件里，可以实现同时启动，如 MySQL 和 phpMyAdmin 的结合_</font>

## Others

一键关闭所有容器
```bash
docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2)
```

进入容器里的命令行
```bash
docker exec -it $容器名 bash
```

加入运行内存和交换内存限制，也可见 [yml 文件](./portainer-ce/README.md) 的设置
```bash
docker update -m ??m --memory-swap ??m $容器名
```

## Ref

- [Overview of Docker Compose](https://docs.docker.com/compose/)
- [docker/compose](https://github.com/docker/compose)
- [docker/awesome-compose](https://github.com/docker/awesome-compose)
- [在 docker compose 版本 3 中指定内存和 CPU 限制](https://stackoom.com/question/2rfvf)

## Changelog

- 211021 15:33 增加内存限制
- 211021 11:34 init
