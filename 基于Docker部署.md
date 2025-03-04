# 下载和安装虚拟机

> VMWare Workstation 可以让我在 windows 系统上创建一个虚拟的, 安装其他操作系统的主机.

- <a href="https://softwareupdate.vmware.com/cds/vmw-desktop/ws/">VMWare Workstation 下载</a>

# 在虚拟机里安装linux

> linux 系统有 debian, ubuntu, centos 等免费开源的系统, 这里采用 `debian12`

1. <a href="https://www.debian.org/">debian 官网</a>, 打开后选择`下载Debian`即可下载一个`.iso`镜像文件
2. 下载完成打开虚拟机软件, `创建新的虚拟机`
   - `自定义(高级)`,
   - `选择虚拟机硬件兼容性`: 类似于选择虚拟机主板规格, 选最新的就行(默认最新, 不改)
   - `安装客户机操作系统`: 选择`稍后安装操作系统`, 在安装前我应该先配置好
   - `选择客户机操作系统`: 客户机操作系统选`Linux`, 如果是最新的 VMware, 就应该有最新的 Debian 选项: `Debian12.x 64 位`
   - `命名虚拟机`: 给虚拟机取个名字, 配置安装路径
   - `处理器配置`: 根据实际情况, 我选择 `2*2` 规格: 两个处理器\*每个处理器两个内核
   - `内存配置`: 根据情况, 我选择 `4g` 内存
   - `网络类型`: 选择 NAT
   - `IO控制器`, `磁盘类型`: 按照推荐的选
   - `选择磁盘`: 创建新的磁盘
   - `指定磁盘容量`: 根据实际情况填写容量, 同时选择`将虚拟磁盘存储为单个文件`
   - `指定磁盘文件`: 将会创建一个`虚拟机名称.vmdk`文件
   - `已经准备好创建虚拟机`: `自定义硬件`, `新CD/DVD`, `使用ISO镜像文件`, 指定到下载好的`debian镜像.iso`
   - `完成`: VMWare 侧边栏多了一个新系统
3. 点击右侧的虚拟机, `运行此虚拟机`, 开始安装

   > 任何时候, 如果想让鼠标脱离虚拟操作系统, 按 `Ctrl+Alt`

   - 选择`install`, 选择中文
   - 配置`root`用户的密码: `#`
   - 配置一个新的用户以及密码: `$`
   - 然后会提示我们选择包的镜像, 如果勾选了中国, 就会罗列出国内的一大帮镜像地址, 我选的清华大学的镜像
   - 再下一步会跳转到一个`选择安装程序`:`空格`勾选, 选上`桌面操作系统`, `Gnome` `SSH service`, `基础操作系统`, 切记选上1,2, 这才有图形化界面, 切忌装逼.
   - 最后是磁盘引导一类的(告诉你你这台电脑<其实是虚拟机>上就装了 debian 一个操作系统,要不要启动的时候就自动进入这个系统呀?选择是即)
   - 最后等待系统安装完成

> 如果安装过程中出现网络无法自动配置问题, 点击虚拟机菜单的编辑, 虚拟网络编辑器, 更改设置, 还原默认设置

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