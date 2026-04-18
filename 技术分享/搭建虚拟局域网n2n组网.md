
## 开始搭建：（root 用户下进行）
*分别在本地linux电脑和公网服务器上编译n2n，步骤如下，前面步骤一样*

1. 安装git
```
apt install git
```
2. 克隆n2n项目
```
git clone https://github.com/ntop/n2n.git
```
3. 进入指定文件夹
```
cd n2n
```
4. 安装autoconf 和C 编译器
```
apt update && apt install -y autoconf build-essential
```
5. 编译项目
```
./autogen.sh
```

```
./configure
```

```
make
```

```
make install
```

6. 服务端启动（搭客户端忽略这步，服务端步骤到此结束）
```
./supernode -p 10086 -f
```

7. 客户端启动测试（搭服务端忽略这步）
```
./edge -c 自定义组名 -a 10.10.10.10 -f -l n2n.moyann.com:10090
```

8. 客户端开机自启
   第一步 Ctrl+C 停掉上步测试，执行下面创建自启服务
```
nano /etc/systemd/system/n2n-edge.service
```
自启服务内容，粘贴以下内容修改组名，Ctrl+x 后输 y 保存退出

```
[Unit]

Description=n2n Edge Service

After=network-online.target docker.service

Wants=network-online.target docker.service

Requires=docker.service

[Service]

Type=simple

# 等 待 Docker 网 络 完 全 启 动

ExecStartPre=/usr/bin/timeout 30 /bin/sh -c 'until docker network ls 2>/dev/null; do sleep >

ExecStart=/root/n2n/edge -c 自定义组名 -a 10.10.10.10 -f -l n2n.moyann.com:10090

Restart=always

RestartSec=5

[Install]

WantedBy=multi-user.target
```

 9. 重载 systemd 配置
 
```
sudo systemctl daemon-reload
```

10. 启动服务
    
```
systemctl start n2n-edge.service
```

11. 设置开机自启
    
```
systemctl enable n2n-edge.service
```

12. 查看服务状态

```
systemctl status n2n-edge.service
```


PC 客户端下载
[EasyN2N（小黄鸭） v3.3 \| Bug侠](https://bugxia.com/357.html)

![组网](https://s3.hi168.com/hi168-31550-1678vkca/图床/组网.png)