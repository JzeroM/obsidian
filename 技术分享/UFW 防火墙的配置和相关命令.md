
## 配置安装
安装：
```shell
apt install ufw
```
UFW 安装之后，默认状态总是 inactive 的。IPv 6 是默认支持的，出站默认规则是 ACCEPT，入站和转发默认规则都是 DROP。通常这就是我们想要的。这里我们首先把 ssh 端口放开，否则在 active 之后，会把我们自己关在门外。
运行：
```shell
sudo ufw allow ssh
```
UFW 会生成关于 22 端口的两条规则，一个是 IPv 4 的，一个是 IPv 6 的。如果你的 ssh 监听端口不是 22，直接 allow 端口号就好，比如 allow 22222，所不同的是，allow ssh 会确定是 tcp，而 allow 端口会添加 TCP 和 UDP 两条规则。

这个时候，enable ufw：
```shell
ufw enable 
```

这个时候，再看一下 ufw 状态：

```shell
ufw status verbose
```
可以看到 ufw 已经启用，默认的入站是 deny，出站全部允许，无转发。

## 查看路由
```shell
iptables -t nat -vnL
```

## 常用规则命令
### 查看防火墙当前状态
```shell
ufw status
```
### 开启防火墙
```shell
ufw enable
```
### 关闭防火墙
```shell
ufw disable
```
### 查看防火墙版本
```shell
ufw version
```
### 默认允许外部访问本机
```shell
ufw default allow
```
### 默认拒绝外部访问主机
```shell
ufw default deny
```
### 允许外部访问 53 端口
```shell
ufw allow 53
```
### 拒绝外部访问 53 端口
```shell
ufw deny 53
```
### 允许某个 IP 地址访问本机所有端口
```shell
ufw allow from 192.168.0.1
```
### 允许指定端口 TCP 协议
```shell
ufw allow 80/tcp
```
### 允许指定端口 UDP 协议
```shell
ufw allow 80/udp
```
### 不允许指定端口 TCP 协议
```shell
ufw delete allow 80/tcp
```
### 不允许指定端口 UDP 协议 
```shell
ufw delete allow 80/udp
```
### 拒绝所有传入并允许所有传出连接
```shell
ufw default allow outgoing
```
```shell
ufw default deny incoming
```
大多数系统只需要打开少量的端口接受传入连接，并且关闭所有剩余的端口。从一个简单的规则基础开始，ufw default 命令可以用于设置对传入和传出连接的默认响应动作。ufw default 也允许使用 reject 参数。
    警告：除非明确设置允许规则，否则配置默认 deny 或 reject 规则会锁定你的服务器。确保在应用默认 deny 或 reject 规则之前，已按照下面的部分配置了 SSH 和其他关键服务的允许规则。
    
## 高级规则命令： 
除了基于端口的允许或阻止，UFW 还允许您按照 IP 地址、子网和 IP 地址/子网/端口的组合来允许/阻止。
### 允许从一个 IP 地址连接
```shell
ufw allow from 123.45.67.89
```
### 允许特定子网的连接
```shell
ufw allow from 123.45.67.89/24
```
### 允许特定 IP/ 端口的组合
```shell
ufw allow from 123.45.67.89 to any port 22 proto tcp
```
 proto tcp 可以删除或者根据你的需求改成 proto udp，所有例子的 allow 都可以根据需要变成 deny。
