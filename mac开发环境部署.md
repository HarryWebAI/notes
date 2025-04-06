# mac 部署开发环境

## 前言

- 刚刚上手mac, 确实有些不习惯, mac 的安装程序通常是 “大蘑菇” `·dmg` 且大多数编程方面的安装包都需要从互联网上下载, 而非 AppStore
- 且下载后其安装过程不可谓不抽象: 就像把安装包放进了一个模拟磁盘, 然后我们需要把真正的程序从这个磁盘拖进“应用程序中”才表示安装成功

## 先把能下载的从 AppStrore 里下载

1. qq
2. 微信

## 然后处理魔法

- 可以从其他系统(windows, 手机)里询问魔法武器的最新网址, 将网址通过微信发送给mac, mac打开再下载

## 开始正式装软件

### 谷歌浏览器

- choreme <https://www.google.com/chrome/>

### vsCode

- 官网 <https://code.visualstudio.com/>
- 安装完成后, 插件直接搜 `syncing`, github 登陆, 即可同步扩展和配置

### Git

- Git <https://git-scm.com/downloads/mac>
  - git 下载需要先安装 HomeBrew <https://brew.sh/>
  - 输入命令: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
  - 然后安装: `brew install git`
  - 可能会踩坑, 说 brew 这个命令 command not found, 是因为没有环境变量
  - 输入命令 `sudo vim ~/.bash_profile`, 切换到超级用户使用 vim 编辑这个配置文件
  - 加入这句话: `export PATH="/opt/homebrew/bin:$PATH"`, 类似于 win 系统把, 把 brew 这个命令加入环境变量
  - 然后 `brew install git`, 安装完成后执行相关配置, 搞定

### 先把笔记项搂下来

- 以后笔记都放在 `~/Documents/notes/`
- 以后所有项目都放在 `~/Documents/projects/`
- 所以克隆笔记只需 `cd ~/Documents`, `git clone <远程仓库地址.git>`

### python

1. python官网 <https://www.python.org/> , Downlodas
2. 打开`.pkg`傻瓜式安装后, 使用 `type -a python3`, 看到3个目录安装完毕, 但是 pip 命令无效(此时必须用pip3)
3. 使用命令 `which python3` & `which pip3` 获取当前路径
4. `sudo vim ~/.bash_profie`:

```conf
export PATH="$PATH:/Library/Frameworks/Python.framework/Versions/3.13/bin/python3"
export PATH="$PATH:/Library/Frameworks/Python.framework/Versions/3.13/bin/pip3"
alias python="/Library/Frameworks/Python.framework/Versions/3.13/bin/python3"
alias pip="/Library/Frameworks/Python.framework/Versions/3.13/bin/pip3"
```

> 疑似有两个命令行配置文件 `~/.bash_profile` &  `.zprofile` , 现在没搞懂, 索性两个一起搞了

- 注意版本 `.../Versions/` 后面是自己安装的python版本
- 前两行是配置 python, pip 的环境变量
- 后两行是别名, 不用再 pip3, 直接 pip 即可

> 后面发现 `brew install python` 可以直接下载安装, 不用去官网了

### node

1. node官网 <https://nodejs.org/zh-cn>
2. 傻瓜式安装后, `node -v`, `npm -v` 命令生效即可

### 安装 python.django 框架, 试着开一个项目

1. `pip install django`
2. 注意这里命令不一样了, 到指定目录下: `python -m django startproject PROJECT_NAME`
3. `Ctrl Shift P`, 选择 python 解释器, 新建虚拟环境, which pip3/python3 确认使用的 pip/python, 装包, 搞定

## 安装和配置cursor

### 安装 cursor

- 官网 <https://www.cursor.com/>

### 安装 navicat 免费版

- 中文官网 <https://www.navicat.com.cn/download/navicat-premium-lite>

### 尝试配置 mcp 服务 browertools

- 参考文档 <https://browsertools.agentdesk.ai/installation>

1. 确保安装 NodeJS
2. 确保安装谷歌浏览器或者谷歌浏览器为内核的浏览器
3. 克隆代码: `git clone https://github.com/AgentDeskAI/browser-tools-mcp.git` (这是一个浏览器插件)
4. 将 `/chrome-extension` 拖到浏览器扩展程序里面 (确保开发者模式打开)
5. 打开cursor, 设置, MCP, `+Add New Gloabl MCP server` , 编辑 `mcp.json`

```json
{
    "mcpServers": {
        "browser-tools": {
            "type": "command",
            "command": "npx @agentdeskai/browser-tools-mcp@1.2.0"
        }
    }
}
```

- 终端运行命令 `npx @agentdeskai/browser-tools-server@1.2.0` 并挂起
- 打开任意网站, 检查, 控制台部分打开 BrowserToolsMCP, 当页面出现 “BrowserToolsMCP已经开始调试此浏览器”, 说明该mcp正确投入使用

> 注意: 浏览器工具服务器在端口 3025 上运行, 请确保终止在此端口上运行的所有进程.

## mac 配置文件默认打开方式

> 安装python后, 默认打开方式是python自带的编辑器, 我需要调整为vsCode打开

- 右键文件, 显示简介, 选择打开方式, 设置为vsCode打开并应用到所有即可

## mac 配置 vscode 新开控制台页也是使用虚拟环境

> 不明原因, 即使配置好了虚拟环境, 再新开终端, 有可能采用全局的python解释器, 所以设置一下

1. `command + ,` 打开设置, 搜索`python.terminal.activateEnvironment`
2. 在`setting.json`中编辑

```json
{
    "workbench.iconTheme": "vscode-icons",
    "terminal.integrated.defaultProfile.windows": "Git Bash",
    "workbench.colorTheme": "Default Dark Modern",
    "explorer.confirmDragAndDrop": false,
    "explorer.confirmDelete": false,
    "python.terminal.launchArgs": [
        
    ],
    // 这里添加这句话
    "python.terminal.activateEnvironment": true
}
````