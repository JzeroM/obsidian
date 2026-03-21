准备工作：clash配置文件、CASpro授权码

注：前提机器必须是飞牛OS系统！
## 部署部分（以下操作在命令行，不用命令也可根据括号内文字直接图形化操作）：
SSH连接飞牛，切换到 root 用户下

root用户下创建文件文件夹server并授权，进入server文件夹：（飞牛我的文件新建 libitv 文件夹，libitv 里面再新建个文件夹 server）
```
mkdir -p /vol1/1000/libitv/server
chmod -R 777 /vol1/1000/libitv/server
cd /vol1/1000/libitv/server
```

Docker创建一个名为 trim-default的自定义桥接网络： (字面意思)
```
docker network create -d bridge trim-default
```
server 文件夹下创建 docker-compose.yml（打开 docker，新建 compose 项目，项目名随便，项目文件夹选前面创建的server 文件夹，创建docker-compose.yml）
```
nano docker-compose.yml
```
 文件内容如下并修改注释部分内容：
```
version: '3.8'
services:
  # Clash 核心服务
  clash:
    image: dreamacro/clash-premium:latest
    container_name: clash
    networks:
      - trim-default
    restart: unless-stopped
    ports:
      - "7890:7890"  # HTTP 代理端口
      - "7891:7891"  # SOCKS5 代理端口
      - "9090:9090"  # 控制端口
    volumes:
      - ./clash/config:/root/.config/clash
    environment:
      TZ: Asia/Shanghai
      EXTERNAL_CONTROLLER: 0.0.0.0:9090

  # Clash 控制面板
  yacd:
    image: haishanh/yacd:latest
    container_name: clash-dashboard
    networks:
      - trim-default
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      YACD_DEFAULT_BACKEND: http://clash:9090
    depends_on:
      - clash

  # Emby 媒体服务器
  emby:
    image: amilys/embyserver:latest # 有神医pro就用最新版本，没有就把latest换成神医免费版支持的版本
    container_name: emby-amilys
    user: '0:0'
    networks:
      - trim-default
    restart: unless-stopped
    devices:
      - /dev/dri:/dev/dri 
    # 新增内存限制配置
    deploy:
      resources:
        limits:
          memory: 12G
    environment:
      UID: 0
      GID: 0
      GIDLIST: 100
      PUID: 1000
      PGID: 1000
      TZ: Asia/Shanghai
      http_proxy: http://clash:7890
      https_proxy: http://clash:7890
      no_proxy: localhost,127.0.0.1,.internal,clash,yacd
    volumes:
      - ./emby/config:/config
      - ./strm:/media
    ports:
      - "8096:8096"
    depends_on:
      - clash

  # 天翼云盘自动同步
  cloud189:
    image: xia1307/cloud189-auto-save-pro:test-0.0.31
    container_name: cloud189pro
    network_mode: "host"  # 改为使用宿主机网络
    restart: unless-stopped
    privileged: true
    volumes:
      - ./cloud189pro/data:/home/data
      - ./strm:/home/strm
    environment:
      PUID: 0
      PGID: 0
      NODE_OPTIONS: --dns-result-order=ipv4first
      SERVICE_CODE: XXXXXXXX这里填授权码
      CASBY_ENABLE: 1
      TZ: Asia/Shanghai
      # 关键修改：设置代理指向宿主机的IP和Clash映射的端口
      http_proxy: http://宿主机的IP:7890
      https_proxy: http://宿主机的IP:7890
      no_proxy: localhost,127.0.0.1,.internal,clash,yacd,v.c.1307x.top

  # MoviePilot 媒体管理
  moviepilot:
    image: jxxghp/moviepilot-v2:latest
    container_name: moviepilot-v2
    stdin_open: true
    tty: true
    networks:
      - trim-default
    ports:
      - "3333:3000"  # 将宿主机端口从3000更改为3333
    volumes:
      - ./strm:/media
      - ./moviepilot/config:/config
      - ./moviepilot/core:/moviepilot/.cache/ms-playwright
      - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
      NGINX_PORT: '3000'
      PORT: '3001'
      PUID: '0'
      PGID: '0'
      UMASK: '000'
      TZ: Asia/Shanghai
      SUPERUSER: admin
      http_proxy: http://clash:7890
      https_proxy: http://clash:7890
      no_proxy: localhost,127.0.0.1,.internal,clash,yacd
    restart: unless-stopped
    depends_on:
      - clash
networks:
  trim-default:
    external: true
```

构建 docker compose 不启动（构建，不勾选自启动）
```
docker-compose up --build
```

进入 clash 配置文件夹修改 config.yaml 文件内容，内容即 clash 配置文件内容并修改两处为 allow-lan: true 和 external-controller: '0.0.0.0:9090'（飞牛商店下载文本编辑器，clash 配置文件在 server 文件夹里面的 clash 文件夹里面的 config 文件夹里，如果文件名不是config.yaml就改成config.yaml，编辑config.yaml 文件）
```
cd clash/config
nano config.yaml
```

返回 server目录并启动 docker compose（再去启动这个 compose）
```
cd /vol1/1000/libitv/server
docker compose up -d
```

## 配置部分：
待定......