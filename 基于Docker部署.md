# 虚拟机, Debian系统安装, 参考基础部署, 略

# 安装Tabby

> 虚拟机运行在 VMware 窗口里, 始终不方便, 不如把虚拟机当成一个远程服务器, 启动后挂起, 我们还是用自己的系统模拟"远程链接"

- 首先要确保安装了 openssh 服务, 在虚拟机按(ctrl+t)打开命令行, 执行以下命令:
   - `sudo systemctl status ssh`: 查看 SSH 服务状态
   - `sudo apt update`, `sudo apt upgrade` :如果没有安装, 先更新apt
   - `sudo apt install openssh-server`: 然后安装 openssh-servcer
   - `sudo systemctl start ssh` 启动服务
   - `sudo systemctl enable ssh` 声明开机自动启动
   - `ip a`: 查看ip, 重点在`2: ens33`这一块, 可以看见网卡的ip地址, 记住它,等会远程连接需要用
- 使用终端软件:<a href="https://github.com/Eugeny/tabby/releases/">Tabby</a>进行远程连接
   - 安装傻瓜式的
   - 安装后打开
   - 标题栏点击:`Profiles & Connections`, 选择`Manage Profiles`, 会打开设置(Settings)页面, 侧边选择`Profiles & Connections`
   - 点击 `+ New` 新建一个 `New Profile`
   - 选择 `SSH connetion`, 取个名字(name), 然后`connetion`处填写上面查到的 ip 地址, 不能选`#root`, 要选`$用户名` 进行登录(登录进去要换root再`su`)

# 安装Docker
- <a href="https://www.runoob.com/docker/debian-docker-install.html">参考文档</a>
- 一次执行以下命令:
   - `sudo apt install apt-transport-https ca-certificates curl software-properties-common`: 安装依赖包
   - `sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc`
   - `sudo chmod a+r /etc/apt/keyrings/docker.asc`: 添加 Docker 官方 GPG 密钥
   - 添加 Docker 仓库
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
   - `sudo apt-get update`, `sudo apt update`: 再次更新, 并更新 APT 包索引
   - `apt-cache policy docker-ce`: 确保你现在从 Docker 官方仓库安装 Docker 而不是 Debian 默认仓库, 显示一长串的`https://download.docker.com/linux/debia`就是正确的
   - `sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin` 安装docker
   - `sudo systemctl start docker`: 启动docker
   - `sudo systemctl enable docker`: 声明开机自动启动
   - `sudo docker --version`: 查看是否安装成功
   - 配置docker加速(相当于编辑 /etc/docker/daemon.json)
```
echo '{
    "registry-mirrors": [
        "https://docker.1ms.run",
        "https://docker.xuanyuan.me"
    ]
}' | sudo tee /etc/docker/daemon.json
```
   - `sudo systemctl restart docker`: 重启docker
   - `sudo docker run hello-world`: 测试是否跑起来, 如果提示`Hello from Docker!`, 表示终于成功了!

> 华夏局域网又把docker墙了, 改 `daemon.json` 也没有用, 只有执行任何需要访问互联网的命令前在镜像前面加上一个镜像站地址

- 比如我要搜索ubuntu, 原命令: `docker search ubuntu`
- 现在只有: `docker search docker.1ms.run/ubuntu`(加上docker.1ms.run/)

# Docker compose
### 安装
- `apt-get install docker-compose-plugin`: apt安装
- `docker compose version`: 查看版本