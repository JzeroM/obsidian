准备工作：CASpro授权码

注：前提机器必须是飞牛OS系统！
## 部署部分（以下操作在命令行，不用命令也可根据括号内文字直接图形化操作）：
SSH连接飞牛，切换到 root 用户下

root用户下创建文件文件夹server并授权，进入server文件夹：（飞牛我的文件新建 libitv 文件夹，libitv 里面再新建个文件夹 server）
```
mkdir -p /vol1/1000/libitv/server
chmod -R 777 /vol1/1000/libitv/server
cd /vol1/1000/libitv/server
```

server 文件夹下创建 docker-compose.yml（打开 docker，新建 compose 项目，项目名随便，项目文件夹选前面创建的server 文件夹，创建docker-compose.yml，修改授权码和飞牛内网 ip）
```
nano docker-compose.yml
```
 文件内容如下并修改注释部分内容：
```
services:
  clash:
    image: metacubex/mihomo:latest
    container_name: clash
    network_mode: bridge
    restart: always
    ports:
      - "7890:7890"
      - "7891:7891"
      - "9090:9090"
    volumes:
      - ./clash/config:/root/.config/mihomo
    environment:
      TZ: Asia/Shanghai
      EXTERNAL_CONTROLLER: 0.0.0.0:9090

  yacd:
    image: haishanh/yacd:latest
    container_name: clash-dashboard
    network_mode: bridge
    restart: always
    ports:
      - "8080:80"
    environment:
      YACD_DEFAULT_BACKEND: http://clash:9090

  emby:
    image: amilys/embyserver:latest
    container_name: emby-amilys
    user: "0:0"
    network_mode: host
    restart: always
    ports:
      - "8096:8096"
    environment:
      UID: 0
      GID: 0
      GIDLIST: 100
      PUID: 1000
      PGID: 1000
      TZ: Asia/Shanghai
      #http_proxy: http://飞牛内网IP:7890
      #https_proxy: http://飞牛内网IP:7890
      #no_proxy: 127.0.0.1,localhost,v.c.1307x.top
    volumes:
      - ./emby/config:/config
      - ./strm:/media

  cloud189:
    image: xia1307/cloud189-auto-save-pro:test-0.0.39
    container_name: cloud189pro
    network_mode: host
    restart: always
    ports:
      - "3000:3000"
      - "8091:8091"
    privileged: true
    volumes:
      - ./cloud189pro/data:/home/data
      - ./strm:/home/strm
    environment:
      PUID: 0
      PGID: 0
      NODE_OPTIONS: --dns-result-order=ipv4first
      SERVICE_CODE: 授权码
      CASBY_ENABLE: 1
      TZ: Asia/Shanghai
      http_proxy: http://飞牛内网IP:7890
      https_proxy: http://飞牛内网IP:7890
      no_proxy: 127.0.0.1,localhost,v.c.1307x.top

  moviepilot:
    image: jxxghp/moviepilot-v2:latest
    container_name: moviepilot-v2
    network_mode: host
    stdin_open: true
    tty: true
    restart: always
    ports:
      - "3002:3002"
    volumes:
      - ./strm:/media
      - ./moviepilot/config:/config
      - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
      MOVIEPILOT_AUTO_UPDATE: true
      NGINX_PORT: "3002"
      PORT: "3001"
      PUID: "0"
      PGID: "0"
      UMASK: "000"
      TZ: Asia/Shanghai
      SUPERUSER: admin
      http_proxy: http://飞牛内网IP:7890
      https_proxy: http://飞牛内网IP:7890
      no_proxy: 127.0.0.1,localhost
```

构建 docker compose 不启动（构建，不勾选自启动）
```
docker-compose up --build
```

进入 clash 配置文件夹修改 config.yaml 文件内容（飞牛商店下载文本编辑器，clash 配置文件在 server 文件夹里面的 clash 文件夹里面的 config 文件夹里，如果文件名不是config.yaml就改成config.yaml，编辑config.yaml 文件）

```
cd clash/config
nano config.yaml
```
config.yaml 的内容如下(替换clash 订阅地址)：

```
mixed-port: 7890
allow-lan: true
bind-address: 0.0.0.0
mode: rule
log-level: info

external-controller: 0.0.0.0:9090
external-ui: ui
external-ui-url: https://github.com/MetaCubeX/metacubexd/archive/refs/heads/gh-pages.zip

tun:
  enable: false

proxy-providers:
  mojito:
    url: "这里填clash订阅地址"
    type: http
    interval: 86400

proxies:
  - name: "直连"
    type: direct

proxy-groups:
  - name: 默认
    type: select
    proxies:
      - 自动选择
      - 直连

  - name: 自动选择
    type: url-test
    use:
      - mojito
    url: http://www.gstatic.com/generate_204
    interval: 300

rules:
  - MATCH,默认
```

返回 server目录并启动 docker compose（再去启动这个 compose）
```
cd /vol1/1000/libitv/server
docker compose up -d
```

## 配置部分：
待定......