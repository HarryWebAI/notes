# 下载：
- 官网下载地址：dev.mysql.com/downloads
    - 选择下载社区版MySQL Community (GPL) Downloads »
    - 选择下载安装程序 MySQL Installer for Windows 
  - 选择更大的那个installer文件，只有几mb的那个是网络安装，一边下载一边装的那种，慢
# 安装：
- 下载好了，打开安装程序。
- 第一步，选择FULL，全装，不折腾
- 第二步，Excute执行安装，这一步在装环境
- 第三步，Excute执行安装，这一步在装程序
- 第四步，开始配置：
- Type and NetWorking，别瞎改，选择develpment Computer，意思是开发用的电脑
- Accounts and Roles，配置账户和角色，mysql有一个大哥账户root，可以对数据库，数据表进行无差别修改，通常开发者用这个就够了。最上面就是在配置root密码，用简单的就行，只要你不把现实的银行卡账号密码往这里面写。（但这个还是要注意，数据库这玩意儿是联了网的，人知道你电脑ip是可以进来的）。
- 之后的几项，别瞎改。最后Excute，执行完再Finish
- 第五步，配置集群路由，不需要。不打勾
- 再下一步，试试root密码，输入后check
- 最后一步，安装完成后打卡workbench?打开mysql shell?可以不打勾
- 搞定
# 配置环境变量：
- 系统设置，搜索环境变量，编辑Path，新建把..\mysql\mysql server 5.7\bin添加到最后
- 【win+r】运行 => 输入【cmd】=>输入【mysql --version】查看版本
- 显示【mysql  Ver xxx...】
# 搞定！
- 命令行里以root登录mysql：
-输入【mysql -u root -p】
- 提示【输入root密码】，输入
- 变成【mysql>】搞定！
- 记得【exit】退出

# 数据库：
- 数据库用来存数据表，数据表用来存数据
- 数据库相当于一个文件夹/数据表相当于Excel文件/Excel文件里面是我们的数据
- mysql可以有很多个数据库，
- 数据库里可以建很多数据表，
- 数据表里可以依照格式存很多数据，
- 这些数据库，数据表可以被【增删改查】
- 表里数据可以也可以被我们【增删改查】
- 编程大多数时候，就是在用计算机语言写代码，帮我们增删改查。

# sql语句示例：
### 建库：
`create database 数据库名称`

### 删库：
`drop database 数据库名称` 

### 查看已有库：
`show database` 

### 建表：
```sql
crate table 数据表名称(
id int DEFUALT NULL,
# 意思是【键名id 类型int 默认 空】
# 字段类型可以参考【www.runoob.com/mysql/mysql-data-types.html】
PRIMARY KEY (配置哪个字段作为主键)
)ENGINE=InnoDB DEFAULT CHARSET=utf-8
```
- 最后一句话的意思是用innoDB引擎，默认编码是utf-8

### 删表：
`drop table 表名`

### 查看表结构：
`desc 表名`

### 查看建表用的sql语句：
`show create table 表名`
### 修改数据表：
```sql
alert table 表名 +
# 重命名数据表
rename to 新表名
# 新增列
add 列名 数据类型
# 删除列
drop 列明
```

### 数据增删改：
- 增加：`insert into 表名 (key1,key2,key3,...) values (v1,v2,v3,...)`
    > 如果每一列都有值，不用写表明后面的括号
- 删除：`delete from 表名 where key = v`, 举例：`delete from student where id = 1`
    > 译为：删除 student 表里 id 为 1 的那一行数据
- 慎用：清空数据表
`truncate table 表名` => 删除同名表，再立刻重建一张一样的表,删除要慎用
- 修改： `update 表名 set key = value where 条件`
- 举例：`update student set name = ‘liudehua’ where id = 1`
    > 译为：更新student 数据表里的信息，找到id为1的那一行数据，将他的name设置为’liudehua’

### 查询数据：
`select * from student where id = 1` => 查询id 为1的所有学生的所有信息
`select * from student where age>18 && age<30` => 查询年龄18~30之间的所有学生的所有信息，也可以写作【...where age between 18 and 30】
`select * from student where name like ‘刘%’` => 查询姓刘的学生，同理，查询名字三个字的学生？【like ___】，查询名字以华结尾的学生？【like %华】
`select * from student where englishscore is not null` => 查询英语成绩不为空的学生

- 所有数据都可以排序：在末尾加上【order by 字段名】，默认升序，如果降序再加【desc】

### 分页查询：
`select * from student limit 0,3` => 查询下标0到下标4的数据

# 关于sql语句：
- 个人认为了解即可，因为现在的高级编程语言，可以在编程时，用更简单的写法，更安全的方法访问数据库进行相关的增删改查。