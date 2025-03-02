# 下载
- <a href="https://softwareupdate.vmware.com/cds/vmw-desktop/ws/">VMWare Workstation下载</a>: 虚拟机程序
- <a href="https://cn.ubuntu.com/download/desktop">Linux_Ubuntu操作系统下载</a>: ubuntu操作系统(比较普遍使用的, 有带图形化界面版本的linux系统, 选LTS版, 即长期支持版)

# 安装
- 先安装虚拟机, 打开虚拟机软件后, "创建新的虚拟机"
- 然后选择ubuntu的镜像, 等他自动开始安装

> 踩了一个小坑: ubuntu装好了没法联网, 直接用**桥接模式**: 虚拟机主页侧边栏, 右键选择安装好的ubuntu系统, 设置, 网络适配器, 勾选桥接模式, 重启系统

# 使用
### Tabby终端
- 使用终端软件:<a href="https://github.com/Eugeny/tabby/releases/">Tabby</a>进行链接, 傻瓜式安装, 略

> 虚拟机挂在后台, 这个软件实现真正的操作

### 建立链接
1. 虚拟机打开ubuntu
2. 确保ubuntu安装了openssh服务, 在虚拟机按(ctrl+t)打开命令行, 执行以下命令:
    - `sudo apt-get update`(更新apt)
    - `sudo apt-get install openssh-server`(安装 openssh-server)
    - `sudo service ssh start` (启动)
    - `sudo service ssh status`(查看状态)
3. 找到本机的ip地址: `ip a`
4. 打开Tabby, 标题栏点击:`Profiles & Connections`, 选择`Manage Profiles`, 会打开设置(Settings)页面, 侧边`Profiles & Connections`
    - `+ New` 新建一个 `New Profile`
    - 选择 `SSH connetion`, 取个名字(name), 然后`connetion`处填写ubuntu的ip地址, 进行登录

### 设置root密码
1. `sudo passwd root` 设置root用户密码
    - 先输入一开始安装ubuntu时的账户密码(也就是当前用户的账号密码)
    - 然后输入两遍 root 密码
2. `su` 进入root用户试试: root用户`#`, 普通用户`$`
3. `su 普通用户名称`, 切换为普通用户

### 如需:设置清华镜像
- <a href="https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/">参考文档</a>
- 进入ubuntu后, `cd /etc/apt` (这个路径下面有一个 sources.list 配置文件, 告诉apt下载各种包的源头在哪里)
    - 找个编辑器打开, 反正我不愿意用vim, 我下载了 sublime text, 然后用命令 `sudo subl /etc/apt/sources.list`打开Tabby
    - 把参考文档那部分配置信息拷贝进去
    - 如果打开只有一行 `# Ubuntu sources have moved to /etc/apt/sources.list.d/ubuntu.sources`, 意思是配置文件移动到了另外一个目录
    - `sudo subl /etc/apt/sources.list.d/ubuntu.sources`, 参考文档说, 这种情况就请用第二个`DEB822 格式（/etc/apt/sources.list.d/ubuntu.sources）`
    - 配置改好了记得 `sudo apt update`

    > 这一操作可以在虚拟机里完成, 因为我安装的就是带ui的ubuntu
