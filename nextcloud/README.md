# nextcloud

## 简介

nextcloud 是个人网盘，主打安全（数据存储在本地），官网介绍说是一个存放您所有数据的安全之家。根据用户自己的要求，从任何设备访问和共享您的文件、日历、联系人、邮件等。

## 目录结构规划

```
usr
└─ local
   └─ docker
      └─ nextcloud
         ├── docker-compose.yml
         └── _data
             ├── apps
             ├── config
             ├── data
             └── themes
```

[_docker-compose.yml_](docker-compose.yml)
```
version: "2"

services:
  db:
    image: mariadb
    network_mode: "bridge"
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW --innodb-file-per-table=1 --skip-innodb-read-only-compressed
    volumes:
      - ./db:/var/lib/mysql
    ports:
      - 49306:3306
    environment:
      - MYSQL_ROOT_PASSWORD=<your_password>
      - MYSQL_PASSWORD=<your_password>
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
    deploy:
        resources:
            limits:
              cpus: '0.05'
              memory: 160M 
            reservations:
              cpus: '0.0050'
              memory: 100M 
  app:
    image: nextcloud
    container_name: nextcloud
    network_mode: "bridge"
    volumes:
      - ./_data:/var/www/html
      - ./_data/apps:/var/www/html/custom_apps
      - ./_data/config:/var/www/html/config
      - ./_data/data:/var/www/html/data
      - ./_data/themes:/var/www/html/themes
    ports:
      - 49166:80
    restart: always
    deploy:
        resources:
            limits:
              cpus: '0.05'
              memory: 400M 
            reservations:
              cpus: '0.0050'
              memory: 300M 
```
_注：_

_deploy 参数后加入了内存和 cpus 限制，部分服务器不支持 CPU 限制，会被自动忽略_
_本文件实现了 mariadb 和 nextcloud 的联动，简单方便，开始前需设置好数据库的密码_

## 使用

### 部署服务

在对应的文件夹下使用 `docker-compose up -d`

预期结果：

```bash
$ docker-compose up -d
Creating mariadb_nc ... done
Creating nextcloud  ... done
```

### 登录页面

浏览器访问 `http://<ip地址>:49166` 登录到管理页面，首次登录创建好用户名和密码即可。

### 查看容器状态

使用命令 `docker ps` 查看容器状态

预期结果：

```bash
$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED             STATUS             PORTS                                                           NAMES
20d4751c5c38   nextcloud                            "/entrypoint.sh apac…"   5 minutes ago   Up 5 minutes           0.0.0.0:49166->80/tcp, :::49166->80/tcp                         nextcloud
396c609bdbb4   mariadb                              "docker-entrypoint.s…"   8 seconds ago   Up 6 seconds           0.0.0.0:49306->3306/tcp, :::49306->3306/tcp                     mariadb_nc
```

### 停止删除容器

在对应的文件夹下使用 `docker-compose down`

预期结果：

```bash
$ docker-compose down
Stopping nextcloud  ... done
Stopping mariadb_nc ... done
Removing nextcloud  ... done
Removing mariadb_nc ... done
``` 

## 番外

### nextcloud 盘的迁移

在其他地方安装的 nextcloud, 如何完整迁移过来？

背景：迁移时报错
```
服务器不能完成您的请求。
如果再次发生这种情况，请将下面的技术详细信息发送给服务器管理员。
更多细节可以在服务器日志中找到。

技术细节
远程地址：172.17.0.1
请求 ID：bcasyorvWIHzPMYNRyO1
```

1. 完整拷贝 nextcloud 容器使用的 `volumes` 文件夹
2. 修改所有权：`chown -R www-data:root ./<文件名>`
3. 进入到 nextcloud 的目录，修改可读权限
```bash
$ chmod 775 ./config -Rf
$ chmod 775 ./apps -Rf
$ chmod 775 ./themes -Rf
```
4. 刷新页面即可

## Ref

- [Nextcloud | Docker Hub](https://hub.docker.com/_/nextcloud)
- [nextcloud/docker: ⛴ Docker image of Nextcloud](https://github.com/nextcloud/docker)
- [TechOverflow](https://techoverflow.net/2021/08/17/how-to-fix-nextcloud-4047-innodb-refuses-to-write-tables-with-row_formatcompressed-or-key_block_size/)
- [NextCloud安装时出现”服务器内部错误” ](https://www.wunote.cn/article/956/)