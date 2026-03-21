1. 更新并下载 curl 和 nano
```shell
sudo apt update && apt upgrade && apt install curl && apt install nano
```
2. 安装允许 apt 通过 HPPTS 使用仓库的依赖包
```shell
sudo apt install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
3. 添加docker官方GPG密钥
```shell
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
4. 配置仓库添加 `Docker` 软件源 （二选一，国外机子选官方源，国内机子选阿里源）
​ 配置仓库 添加`Docker`软件源 （国外官方源）
```shell
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
配置仓库 添加`Docker`软件源 （国内镜像阿里源）
```shell
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/debian \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
6. -再次更新apt包索引
```shell
sudo apt update
```
7. -安装最新版本的 `Docker Engine`,`containerd`,`Docker Compose`
```shell
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
8. 查看 docker 版本
```shell
docker version
```
9. 修改 SSH 默认端口
```shell
nano /etc/ssh/sshd_config
```
找到 Port 22，将默认的 22 端口修改为指定端口即可
10. 更新
```shell
apt update
```
11. 启用 BBR

```shell
nano /etc/sysctl.conf
```
12. 在尾部追加

```shell
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```
13. 刷新配置使其生效

```shell
sysctl -p
```
14. 检查 BBR 是否开启
```shell
sysctl net.ipv4.tcp_available_congestion_control
```
显示net.ipv4.tcp_available_congestion_control = reno cubic bbr则表示安装成功

15. 在 Linux 远程服务器生成密钥
- 先运行这个命令

```shell
ssh-keygen -t rsa 
```

会显示如下：
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):                   //直接回车
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase):                                    //输入密钥密码
Enter same passphrase again:                                                                 //重复密钥密码
Your identification has been saved in /root/.ssh/id_rsa.         //提示公钥和私钥已经存放在/root/.ssh/目录下
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
15:23:a1:41:90:10:05:29:4c:d6:c0:11:61:13:23:dd root@vpser.net
The key's randomart image is:
+--[ RSA 2048]----+
|=&@Bo+o o.o      |
|=o=.E  o . o     |
| .    .   .      |
|         .       |
|        S        |
|                 |
|                 |
|                 |
|                 |
+-----------------+
root@vpser:~#
将/root/.ssh/下面的id_rsa和id_rsd.pub妥善保存。

 - 将密钥添加到远程 Linux 服务器:
 
用 winscp，将 id_rsa.pub 文件上传到/root/.ssh/下面（如果没有则创建此目录），并重命名为：authorized_keys 如果是在 Linux 服务器上生成的密钥直接执行：
```shell
mv /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys
```
再执行：
```shell
chmod 600 /root/.ssh/authorized_keys 
```
修改权限。

- 修改 sshd_config 文件，将 RSAAuthentication 和 PubkeyAuthentication 后面的值都改成 yes ，保存。如果这两项前面有 # 符号说明是注释掉的，需要去掉 # 符号。
```shell
nano /etc/ssh/sshd_config
```

- 重启 sshd 服务，Debian/Ubuntu 执行：
```shell
/etc/init.d/ssh restart
```
- 修改/etc/ssh/sshd_config 文件:
```
nano /etc/ssh/sshd_config
```
将PasswordAuthentication yes 修改成 PasswordAuthentication no

- 重启 sshd 服务，Debian/Ubuntu 执行
```shell
/etc/init.d/ssh restart
```

