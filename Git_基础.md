# 常用 git 命令

- 配置全局用户名和邮箱
  > git config --global user.name "用户名"
  >
  > git config --global user.email "邮箱"
- 本地初始化仓库
  > git init
- 添加改动文件，''.''表示所有文件
  > git add .
- 提交改动，-m "后接参数为本次提交的备注说明
  > git commit -m "first commit"
- 配置远程仓库地址
  > git remote add origin 项目地址.git
- 推送更新到远程仓库
  > git push -u origin "master"

# 后续 git 操作

- 每次修改文件内容后，需要添加变更到缓存，提交，推送
  > git add .
  >
  > git commit -m "简单说明本次提交修改了什么内容"
  >
  > git push

# commit 的注释修改起来很麻烦

> git commit --amend -m "在 push 之前可以修改最新一次 commit -m 的备注"

- 已经推送的远程仓库再想改就来不及了，push 之前小心再小心。
- commit -m "注释"，这个注释有时候容易忘记写，或者写错，可以用：

  > git commit --amend

  > 用打开最近一次的提交文档，【i】进入编辑，第一行就是注释，再【esc】【:wq】保存退出

  > git log #查看提交日志

  > git rebase -i HEAD~数字 #展示最近 2 次注释

  > git commit --amend #重复上面的操作

  > git rebase --continue #修改成功

  > 如果已经 push 了，就只有 pull 下来，改完注释后，再

  > git push --force #强制推送，所以推之前小心点

# .gitignore

> 主要作用是让 git 知道哪些文件不用追踪, 通常包括: 媒体文件, 各种包(只需要项目使用者通过不同的依赖管理工具自己装)

- 典型例子:

```conf
database.cnf              # 这是我项目数据库的配置文件, 我不希望它分享在GitHub中
smtp.cnf                  # 这是我项目smtp服务的配置, 我也不希望它出现在GitHub中
media/*                   # 这是我项目中用于存储用户所上传图片的文件夹, 我不希望git追踪内部任何文件, 都是图片太占空间
!media/.gitkeep           # 但是我又希望这个文件夹存在, 所以我告诉git, 你不能忽略 media/.gitkeep, 虽然它是个空文件
```

> 这样一来, 两个配置文件没有被追踪, 一个 `~/media/` 文件夹被保存了下来

- 之前我忘了声明取消追踪`media/*`, 已经有一些图片被上传了去 github, 怎么办? `git rm -r --cached media`
