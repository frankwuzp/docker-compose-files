# mysql

## 目录结构规划

```
docker
└─ mysql
  ├─ docker-compose.yml
  ├─ conf
  │  └─ my.cnf
  └─ db
```

[_docker-compose.yaml_](docker-compose.yaml)
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
```

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