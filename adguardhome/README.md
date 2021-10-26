# AdGuardHome

## 简介

AdGuardHome 是一款全网络软件，用于阻止广告和跟踪。设置后，它将涵盖您所有的家庭设备，您不需要任何客户端软件。

## 目录结构规划

```
docker
└─ pihole
   ├── docker-compose.yml
   ├── conf
   └── work
```

[_docker-compose.yml_](docker-compose.yml)
```
version: "3"

# More info at https://github.com/AdguardTeam/AdGuardHome/wiki/Docker
services:
  adguardhome:
    container_name: adguardhome
    network_mode: "bridge"
    image: adguard/adguardhome:latest
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp"
      - "68:68/udp"
      - "80:80/tcp"
      - "3000:3000/tcp"
    # Volumes store your data between container upgrades
    volumes:
      - './work/:/opt/adguardhome/work/'
      - './conf/:/opt/adguardhome/conf/'
    restart: unless-stopped
    deploy:
        resources:
            limits:
              cpus: '0.05'
              memory: 50M
            reservations:
              cpus: '0.0005'
              memory: 40M
```
_注：_

_deploy 参数后加入了内存限制，表示一般情况下容器的 cpus 使用被限制在 5%, 内存为 50 MB ，当服务器资源紧张时，cpus 占用变为 0.05%, 内存占用被限制为 40 MB_

_因未加入交换内存限制，部署时加上 `--compatibility` 兼容性参数，类似于 `docker-compose --compatibility up -d`_

**端口说明**

```txt
-p 53:53/tcp -p 53:53/udp：纯 DNS。 
-p 67:67/udp -p 68:68/tcp -p 68:68/udp：如果您打算使用 AdGuard Home 作为 DHCP 服务器，请添加。 
-p 80:80/tcp -p 443:443/tcp -p 443:443/udp -p 3000:3000/tcp：如果您要使用 AdGuard Home 的管理面板以及将 AdGuard Home 作为 HTTPS 运行，请添加DNS-over-HTTPS 服务器。 
-p 853:853/tcp：如果您要将 AdGuard Home 作为 DNS-over-TLS 服务器运行，请添加。 
-p 784:784/udp -p 853:853/udp -p 8853:8853/udp：如果您要将 AdGuard Home 作为 DNS-over-QUIC 服务器运行，请添加。你可以只留下其中的一两个。 
-p 5443:5443/tcp -p 5443:5443/udp：如果您要将 AdGuard Home 作为 DNSCrypt 服务器运行，请添加。
```

初次使用：`服务器 ip 地址:3000` 打开初始设置页面，跟随引导即可。
## 使用

### 部署服务

在对应的文件夹下使用 `docker-compose up -d`

预期结果：

```bash
$ docker-compose up -d
[+] Running 1/1
 ⠿ Container adguardhome  Started
```

### 查看容器状态

使用命令 `docker ps` 查看容器状态

预期结果：

```bash
$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED             STATUS             PORTS                                                           NAMES
bff96313aa55   adguard/adguardhome:latest     "/opt/adguardhome/Ad…"               11 seconds ago   Up 7 seconds (health: starting)   0.0.0.0:53->53/udp, :::53->53/udp, 0.0.0.0:53->53/tcp, :::53->53/tcp, 443/tcp, 0.0.0.0:80->80/tcp, :::80->80/tcp, 853/tcp, 3001/tcp, 443/udp, 5443/tcp, 5443/udp, 0.0.0.0:3000->3000/tcp, 0.0.0.0:67->67/udp, :::3000->3000/tcp, :::67->67/udp, 6060/tcp, 8853/udp, 0.0.0.0:69->68/udp, :::69->68/udp                                        pihole
```

### 常用拦截规则

**大陆**

| 规则名 | 简介 | 地址 |
|---|---|---|
| [AdGuard DNS Filter](https://github.com/AdguardTeam/AdguardSDNSFilter) | AdGuard 官方维护的广告规则，涵盖多种过滤规则	| https://raw.githubusercontent.com/AdguardTeam/FiltersRegistry/master/filters/filter_15_DnsFilter/filter.txt |
| [anti-AD](https://github.com/privacy-protection-tools/anti-AD) | 命中率高、兼容性强	| https://anti-ad.net/easylist.txt |
| [Halflife](https://gitee.com/halflife/list/issues/I1CV06) | 涵盖了 EasyList China、EasyList Lite、CJX ’s Annoyance、乘风视频过滤规则，以及补充的其它规则	| https://gitee.com/halflife/list/raw/master/ad.txt |

**境外**

以上规则，再加上 [Fanboy’s Annoyances List](https://secure.fanboy.co.nz/fanboy-annoyance.txt) 去除页面弹窗广告规则,[Adblock Warning Removal List](https://easylist-downloads.adblockplus.org/antiadblockfilters.txt
)	去除禁止广告拦截提示规则

### 停止删除容器

在对应的文件夹下使用 `docker-compose down`

预期结果：

```bash
$ docker-compose down
[+] Running 1/1
 ⠿ Container adguardhome        Removed
``` 

## Ref

- [Docker · AdGuardHome Wiki](https://github.com/AdguardTeam/AdGuardHome/wiki/Docker)
- [adguard/adguardhome | Docker Hub](https://hub.docker.com/r/adguard/adguardhome)
- [AdGuard Home 安装及使用指北 - 少数派](https://sspai.com/post/63088)
- [AdGuard Home 自建 DNS 防污染、去广告教程 #2 - 优化增强设置详解](https://p3terx.com/archives/use-adguard-home-to-build-dns-to-prevent-pollution-and-remove-ads-2.html)