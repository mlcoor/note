## 更换镜像源

在换源之前先更新一下。

```bash
# 更新
sudo apt update
```

在这里更换的是南京大学镜像源。

```bash
sudo apt install apt-transport-https ca-certificates
```

编辑软件源配置文件

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
sudo vi /etc/apt/sources.list
```

粘贴下面的内容到配置文件，使用的是 testing 源。

```
deb https://mirror.nju.edu.cn/debian/ testing main contrib non-free
# deb-src https://mirror.nju.edu.cn/debian/ testing main contrib non-free
deb https://mirror.nju.edu.cn/debian/ testing-updates main contrib non-free
# deb-src https://mirror.nju.edu.cn/debian/ testing-updates main contrib non-free
deb https://mirror.nju.edu.cn/debian/ testing-backports main contrib non-free
# deb-src https://mirror.nju.edu.cn/debian/ testing-backports main contrib non-free
deb https://mirror.nju.edu.cn/debian-security testing-security main contrib non-free
# deb-src https://mirror.nju.edu.cn/debian-security testing-security main contrib non-free
```

再次更新

```bash
sudo apt update
```

## 设置中文环境

```bash
sudo apt install locales
sudo dpkg-reconfigure locales
```

然后选择 zh_CN.UFT-8。

## WSL2 内设置代理

我是选择使用 proxychains-ng 而不是设置 `export http_proxy...` 之类的方式。

首先是 Windows 在 WSL2 中的 IP 是动态变化的，要获得该 IP 地址可以通过以下命令

```bash
cat /etc/resolv.conf | grep nameserver | awk '{print $2}'
```

安装 proxychains-ng：

```bash
sudo apt install proxychains4
```

创建配置文件

```bash
mkdir ~/.proxychains
sudo cp /etc/proxychains4.conf ~/.proxychains/proxychains.conf
sudo chown $(whoami) ~/.proxychains/proxychains.conf
sudo chgrp $(whoami) ~/.proxychains/proxychains.conf
```

确保运行在 Windows 上的代理软件允许局域网访问，同时记录 socks5 的端口，修改 `.bashrc` 文件，在文件加入下面内容 ，记得修改端口号。

```bash
# Get the IP of Windows
WIN_IP=$(cat /etc/resolv.conf | grep "nameserver" | awk '{print $2}')
# Delete the old configuration of socks5
sed -i '/socks5/d' ~/.proxychains/proxychains.conf
# Add new configuration of socks5
sed -i '$a socks5 '"${WIN_IP}"' 7890' ~/.proxychains/proxychains.conf
```

更新 bash 配置。

```bash
source .bashrc
```
