# Pi-hole

## 简介

Pi-hole® 是一个 DNS 拦截器，无需安装任何客户端软件即可保护您的设备免受不需要的内容的侵害。

## 目录结构规划

```
docker
└─ pihole
   ├── docker-compose.yml
   ├── etc-dnsmasq.d
   ├── etc-pihole
   ├── www-admin
   └── www-pihole
```

[_docker-compose.yml_](docker-compose.yml)
```
version: "3"

# More info at https://github.com/pi-hole/docker-pi-hole/ and https://docs.pi-hole.net/
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp"
      - "80:80/tcp"
    environment:
      TZ: 'Asia/Shanghai'
      WEBPASSWORD: your_password
      # WEBPASSWORD: 'set a secure password here or it will be random'
    # Volumes store your data between container upgrades
    volumes:
      - './etc-pihole/:/etc/pihole/'
      - './etc-dnsmasq.d/:/etc/dnsmasq.d/'
      - './www-pihole/:/var/www/html/pihole'
      - './www-admin/:/var/www/html/admin'
      # replaced by Chinese-patch
    # Recommended but not required (DHCP needs NET_ADMIN)
    #   https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
    deploy:
        resources:
            limits:
              cpus: '0.05'
              memory: 50M
            reservations:
              cpus: '0.0005'
              memory: 30M
```
_注：_

_deploy 参数后加入了内存限制，表示一般情况下容器的 cpus 使用被限制在 5%, 内存为 50 MB ，当服务器资源紧张时，cpus 占用变为 0.05%, 内存占用被限制为 30 MB_

_因未加入交换内存限制，部署时加上 `--compatibility` 兼容性参数，类似于 `docker-compose --compatibility up -d`_

## 汉化说明

文件目录中，`www-admin` 和 'www-pihole' 文件夹用于存放对应的 [admin](./chinese-patch/admin/) 和 [pihole](./chinese-patch/pihole/) 汉化包，本仓库已将 [MrHousehao/Pi-hole-Chinese](https://github.com/MrHousehao/Pi-hole-Chinese) [liyachvn/pi-hole-chinese](https://github.com/liyachvn/pi-hole-chinese) 二者合并，可直接拷贝到对应的文件夹。

## 使用

### 部署服务

在对应的文件夹下使用 `docker-compose up -d`

预期结果：

```bash
docker-compose up -d
[+] Running 1/1
 ⠿ Container pihole  Started
```

### Pi-hole 登录密码

浏览器输入链接 `http://pi.hole/admin` 或 `http://localhost:端口号/admin` 登录管理后台，其中 `端口号` 设定的是 `80` 则可省去。

修改密码的命令：`docker exec -it pihole pihole -a -p <newpassword>`

### 查看容器状态

使用命令 `docker ps` 查看容器状态

预期结果：

```bash
$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED             STATUS             PORTS                                                           NAMES
bff96313aa55   pihole/pihole:latest     "/s6-init"               11 seconds ago   Up 7 seconds (health: starting)   0.0.0.0:553->53/tcp, 0.0.0.0:53->53/udp, :::53->53/tcp, :::53->53/udp, 0.0.0.0:67->67/udp, :::67->67/udp, 0.0.0.0:80->80/tcp, :::80->80/tcp                                                                                                                                                    pihole
```

### 停止删除容器

在对应的文件夹下使用 `docker-compose down`

预期结果：

```bash
$ docker-compose down
[+] Running 2/2
 ⠿ Container pihole        Removed
 ⠿ Network pihole_default  Removed
``` 

## Ref

- [pihole/pihole - Docker Hub](https://hub.docker.com/r/pihole/pihole)
- [pi-hole/docker-pi-hole](https://github.com/pi-hole/docker-pi-hole)
- [MrHousehao/Pi-hole-Chinese](https://github.com/MrHousehao/Pi-hole-Chinese)
- [liyachvn/pi-hole-chinese](https://github.com/liyachvn/pi-hole-chinese)
