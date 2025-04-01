# windows开发环境部署

> 这篇文章记录: 针对自己当前技术栈在windows下配置开发环境的过程

## 准备

1. 重装了电脑, 卸载了没用的预装软件, 装一些必要的软件(wps, qq, 微信, 7zip...)
2. "梯子魔法", 略
3. 配置输入法, 我喜欢"中文输入时使用英文标点"

## 2, vsCode

1. 下载vsCode <https://code.visualstudio.com/download>
2. 安装一系列插件(使用Syncing备份)

## 3, node环境

1. 安装nvm <https://github.com/coreybutler/nvm-windows/releases>
    - 一直下一步, 不要改路径
    - 安装后重启(如果 `node -v` 失效)

2. cmd > `nvm install node`
    - nvm有问题的话, 可以 <https://nodejs.org/en/download/> 直接官网下载.msi
    - cmd > `node -v`, `npm -v`, 显示版本则成功

## 3, python环境

1. 下载python <https://www.python.org/downloads/>
    - 无脑安装
    - cmd > `python --version`, 显示版本则成功

## 4, 安装git

1. 下载git <https://git-scm.com/downloads/win>
    - 记得勾选gitBash, 无脑安装
2. 配置git:

```bash
git config --global user.name "name"

git config --global user.email "email"
```

## 5, 安装mysql

1. 下载 mysql <https://dev.mysql.com/downloads/mysql/>
    - 记得设置root密码, 无脑安装

## 6, 安装redis

1. 下载 redis for windows <https://github.com/redis-windows/redis-windows/releases>
    - 压缩包形式, 使用时需打开 `redis-server.exe`

## 7, postman

1. 下载 post man <https://www.postman.com/downloads/>
    - 安装后需要登录 (用谷歌邮箱登录)
