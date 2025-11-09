准备工作：clash 配置文件、CASpro授权码、神医助手pro授权文件、mp认证信息

注：由于 mediahelp 删库，自行想办法 pull docker 镜像，或者舍弃夸克部分（要同时删除 compose 配置文件里相关部分）！

SSH 连接 nas 或者服务器，切换到 root 用户下

Root用户下创建文件文件夹server 和文件夹strm（存放 strm 文件） 并授权，进入server文件夹：
```
mkdir /vol1/1000/server && mkdir /vol1/1000/strm
chmod -R 777 /vol1/1000/server && chmod -R 777 /vol1/1000/strm
cd /vol1/1000/server
```

创建一个名为 trim-default的自定义桥接网络： 
```
docker network create -d bridge trim-default
```

server 文件夹下创建 docker-compose.yml 文件内容如下并修改注释部分内容：
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
      - "6666:80"
    environment:
      YACD_DEFAULT_BACKEND: http://clash:9090
    depends_on:
      - clash

  # CloudSaver 服务
  cloudsaver:
    image: jiangrui1994/cloudsaver:latest
    container_name: cloud-saver
    networks:
      - trim-default
    ports:
      - "8008:8008"
    environment:
      TZ: Asia/Shanghai
      http_proxy: http://clash:7890
      https_proxy: http://clash:7890
      no_proxy: localhost,127.0.0.1,.internal,clash,yacd
    volumes:
      - ./cloudsaver/data:/app/data
      - ./cloudsaver/config:/app/config
    restart: unless-stopped
    depends_on:
      - clash

  # Emby 媒体服务器
  emby:
    image: amilys/embyserver:latest
    container_name: emby-amilys
    user: '0:0'
    networks:
      - trim-default
    restart: unless-stopped
    devices:
      - /dev/dri:/dev/dri
    environment:
      UID: 0
      GID: 0
      GIDLIST: 100
      PUID: 0
      PGID: 0
      TZ: Asia/Shanghai
      http_proxy: http://clash:7890
      https_proxy: http://clash:7890
      no_proxy: localhost,127.0.0.1,.internal,clash,yacd
    volumes:
      - ./emby/config:/config
      - ../strm:/media
    ports:
      - "8096:8096"
    depends_on:
      - clash

  # 天翼云盘自动同步
  cloud189:
    image: xia1307/cloud189-auto-save-pro:latest
    container_name: cloud189pro
    networks:
      - trim-default
    user: '0:0'
    restart: unless-stopped
    privileged: true
    ports:
      - "3333:3000"
      - "8091:8091"
    volumes:
      - ./cloud189pro/data:/home/data
      - ../strm:/home/strm
    environment:
      PUID: 0
      PGID: 0
      NODE_OPTIONS: --dns-result-order=ipv4first
      SERVICE_CODE: 这里填CASpro的授权码
      TZ: Asia/Shanghai
      http_proxy: http://clash:7890
      https_proxy: http://clash:7890
      no_proxy: localhost,127.0.0.1,.internal,clash,yacd
    depends_on:
      - clash

  # 媒体助手
  mediahelper:
    image: rongyunmu/mediahelp
    container_name: mediahelper
    networks:
      - trim-default
    restart: unless-stopped
    ports:
      - "3300:80"
      - "8092:8091"
    volumes:
      - ./mediahelp/db:/app/backend/db
      - ../strm/云视听:/app/backend/strm
    environment:
      PUID: 0
      PGID: 0
      TZ: Asia/Shanghai
      http_proxy: http://clash:7890
      https_proxy: http://clash:7890
      no_proxy: localhost,127.0.0.1,.internal,clash,yacd
    depends_on:
      - clash

  # MoviePilot 媒体管理
  moviepilot:
    image: jxxghp/moviepilot-v2:latest
    container_name: moviepilot-v2
    stdin_open: true
    tty: true
    networks:
      - trim-default
    ports:
      - "3000:3000"
    volumes:
      - ../strm:/media
      - ./moviepilot2/config:/config
      - ./moviepilot2/core:/moviepilot/.cache/ms-playwright
      - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
      NGINX_PORT: '3000'
      PORT: '3001'
      PUID: '0'
      PGID: '0'
      UMASK: '000'
      TZ: Asia/Shanghai
      AUTH_SITE: xxxx #mp认证站点名称;如果不是AUTH_SITE，前面也改成认证给你的属性名
      ZMPT_UID: xxxx  #认证UID;如果不是ZMPT_UID，前面也改成认证给你的属性名
      ZMPT_PASSKEY: xxx #认证秘钥;如果不是ZMPT_PASSKEY，前面也改成认证给你的属性名
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

构建 docker compose 不启动
```
docker-compose up --build
```

进入 clash 配置文件夹修改 config.yaml 文件内容，内容即 clash 配置文件内容并修改两处为 allow-lan: true 和 external-controller: '0.0.0.0:9090'
```
cd clash/config
nano config.yaml
```

返回 server目录并启动 docker compose
```
cd /vol1/1000/server
docker compose up -d
```
