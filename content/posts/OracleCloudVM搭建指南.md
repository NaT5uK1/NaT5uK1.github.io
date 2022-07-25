---
title: "OracleCloudVM搭建指南"
date: 2022-07-25T09:20:56+08:00
draft: false
---



# Oracle Cloud VM

## 创建实例

更改默认镜像为`ubuntu20.04`，用户名默认为`ubuntu`

若创建其他镜像，用户名默认为`opc`

## 添加SSH密钥对

### 生成密钥对

1. 在【实例】-【资源】-【控制台连接】中，【创建本地连接】
2. 默认选择【为我生成密钥对】
3. 【保存私有秘钥】【保存公共秘钥】
4. 将私钥和公钥重命名为`oracle` `oracle.pub`（任意合法文件名即可）

### 管理密钥对

1. 将`oracle` `oracle.pub`复制到`~/.ssh`中

2. 为`oracle`添加权限：

   ```shell
   chmod 400 ~/.ssh/oracle
   ```

3. 由于通常情况下本机已有名为`id_rsa` `id_rsa.pub`的密钥对存在，所以需要配置多秘钥管理：

   ```SHELL
   cd ~/.ssh
   vim config
   ```

   `~/.ssh/config` 配置如下：

   ```SHELL
   Host host
       HostName host
       IdentityFile ~/.ssh/oracle
   
   Host github.com
       HostName github.com
       User git
       IdentityFile ~/.ssh/id_rsa
   ```

   + 上面`host`需替换为自己的服务器ip或域名

### 上传公钥

1. 在【实例】-【资源】-【控制台连接】中，【创建本地连接】
2. 选择【上载公共秘钥文件(.pub)】
3. 将`oracle.pub`拖入秘钥框
4. 【创建控制台连接】

## 连接SSH服务

```shell
ssh ubuntu@host
```

+ 提示将host加入`known_hosts`中按y

## 更新apt

```shell
sudo apt update
```

## 安装图形界面xfce4

```
sudo apt install xfce4 xfce4-goodies
```

选择`gdm3`

## 创建VNC连接

### 服务端

1. 安装tightvncserver

```shell
sudo apt install tightvncserver
```

2. 启动vncserver并设置连接密码

```shell
vncserver
```

```shell
# Output
You will require a password to access your desktops.

Password:
Verify:

# 此处务必选择n，否则无法执行更改操作
Would you like to enter a view-only password (y/n)? n
xauth:  file /home/ubuntu/.Xauthority does not exist

New 'X' desktop is hostname:1

Creating default startup script /home/ubuntu/.vnc/xstartup
Starting applications specified in /home/ubuntu/.vnc/xstartup
Log file is /home/ubuntu/.vnc/hostname:1.log
```

3. 需要修改vnc连接密码时（可选）:

```shell
vncpasswd
```

4. 需要停止vncserver以完成后续配置

```shell
vncserver -kill :1
```

```shell
# Output
Killing Xtightvnc process ID 29053
```

5. 备份`~/.vnc/xstartup`

```shell
mv ~/.vnc/xstartup ~/.vnc/xstartup.bak
```

6. 修改`~/.vnc/xstartup`

```shell
vim ~/.vnc/xstartup
```

添加如下内容：

```shell
#!/bin/bash
xrdb $HOME/.Xresources
startxfce4 &
```

7. 为`xstartup`添加权限

```shell
chmod +x ~/.vnc/xstartup
```

8. 重启vnc服务

```shell
vncserver -localhost
```

```shell
# output
New 'X' desktop is hostname:1

Starting applications specified in /home/ubuntu/.vnc/xstartup
Log file is /home/ubuntu/.vnc/hostname:1.log
```

### 客户端

1. 创建一个SSH隧道用于安全地连接到vncserver

```shell
ssh -L 59000:localhost:5901 -C -N -l ubuntu host
```

此时保持客户端shell会话

2. MacOS下，`cmd+space`搜索 `Screen sharing.app`并打开，连接ip地址为`localhost:59000`，输入vnc连接密码，显示出xfce4图形界面即表示成功，Windows下可以使用`PuTTY`

## 将VNC配置为系统服务

### 服务端

1. 添加vncserver服务配置文件

```shell
sudo vim /etc/systemd/system/vncserver@.service
```

添加以下配置：

```shell
[Unit]
Description=Start TightVNC server at startup
After=syslog.target network.target

[Service]
Type=forking
User=ubuntu
Group=ubuntu
WorkingDirectory=/home/ubuntu

PIDFile=/home/ubuntu/.vnc/%H:%i.pid
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1
ExecStart=/usr/bin/vncserver -depth 24 -geometry 1280x800 -localhost :%i
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target
```

2. 重新加载单元文件

```shell
sudo systemctl daemon-reload
```

3. 启动单元文件

```shell
sudo systemctl enable vncserver@1.service
```

4. 如果vncsever在运行，停止当前服务

```shell
vncserver -kill :1
```

5. 启动vncserver服务

```shell
sudo systemctl start vncserver@1
```

6. 验证服务启动状态

```shell
sudo systemctl status vncserver@1
```

```shell
# output
vncserver@1.service - Start TightVNC server at startup
     Loaded: loaded (/etc/systemd/system/vncserver@.service; enabled; vendor pre>
     Active: active (running) since Sat 2022-04-02 20:21:30 UTC; 4s ago
    Process: 29620 ExecStartPre=/usr/bin/vncserver -kill :1 > /dev/null 2>&1 (co>
    Process: 29624 ExecStart=/usr/bin/vncserver -depth 24 -geometry 1280x800 -lo>
   Main PID: 29632 (Xtightvnc)
```



## 更新

```
sudo apt update
```

## Firewall

+ 查看防火墙状态

```
sudo ufw status

Status: inactive	#表示防火墙未开启
```

+ 禁用/启用防火墙

```
sudo ufw disable
sudo ufw enable
```

+ 添加规则

```
sudo ufw allow 22/tcp
sudo ufw deny 8080/tcp
```



## iptables

+ 开放所有端口

```
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
iptables -F
```



## 终端美化

+ 安装Zsh

```
sudo apt install zsh
```

+ 查询Zsh版本

```
zsh --version
```

+ 安装oh-my-zsh

```
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

Do you want to change your default shell to zsh?[Y/n] y
```

+ 安装zsh插件

```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

+ 配置oh-my-zsh

```
sudo vim ~/.zshrc
```

```
#~/.zshrc
ZSH_THEME="agnoster"
plugins=(git zsh-syntax-highlighting zsh-autosuggestions)
```

```
source ~/.zshrc #应用配置
```



## Web Server

### Nginx

+ 安装Nginx

```
sudo apt install nginx
```

+ 查询服务状态

```
sudo systemctl status nginx
```

+ 创建自定义配置

```
sudo vim /etc/nginx/conf.d/webserver.conf
```

以`*.conf`结尾才能被主配置文件识别，或者手动修改`/etc/nginx/nginx.conf`中的`http`的`include`字段

```
server {
        listen       80;
        server_name  your_domain;
        location / {
            root   /usr/share/nginx/dist;
            index  index.html index.htm;
        }
}
```

> 记得域名解析

+ 上传服务文件

```
#本机上传到临时目录
scp -r ./dist/ ubuntu@your_ip:/tmp

#远程主机移动到nginx目录
sudo mv /tmp/dist /usr/share/nginx
```

+ 删除默认配置

```
sudo rm /etc/nginx/sites-enabled/default
```

+ 重新加载

```
sudo nginx -s reload
```

+ 测试服务

```
curl 127.0.0.1
```



## Python3

+ 版本查询

```
python3 -V
```

+ 安装pip

```
sudo apt install python3-pip
```



## OpenSSL

+ 更新

```
sudo apt upgrade openssl
```

+ 解决error while loading shared libraries: libssl.so.1.1: cannot open shared object file: No such file or directory

```
wget https://www.openssl.org/source/openssl-1.1.1o.tar.gz
tar -xzf openssl-1.1.1o.tar.gz
cd openssl-1.1.1o
./config
make
make test
sudo make install

sudo find / -name libssl.so.1.1
sudo ln -s /usr/local/lib/libssl.so.1.1 /usr/lib/libssl.so.1.1
sudo find / -name libcrypto.so.1.1
sudo ln -s /usr/local/lib/libcrypto.so.1.1 /usr/lib/libcrypto.so.1.1
```

 

## 时区

+ 查看时区

```
date -R
```

+ 更改时区

```
sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

