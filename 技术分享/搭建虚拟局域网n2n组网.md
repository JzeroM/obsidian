## 准备工作：
*需要一台有公网ip的服务器并开放tcp协议端口8080*
## 开始搭建：
*分别在本地linux电脑和公网服务器上编译n2n，步骤如下*

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
apt update
apt install autoconf
apt install build-essential
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

6. 服务端启动（搭客户端忽略这步）
```
./supernode -p 10086 -f
```

7. 客户端启动
```
./edge -c jaychou -a 10.10.10.10 -f -l n2n.moyann.com:10090
```

PC 客户端下载
[EasyN2N（小黄鸭） v3.3 \| Bug侠](https://bugxia.com/357.html)
