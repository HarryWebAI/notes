# 第一个程序 helloworld

```python
# python是面向对象的解释型编程语言
# python是强类型的动态脚本语言

print("hello world!") # print()函数：打印

# 【常见BUG】
    # print（“”） ！错误：不能使用全角符号（即中文符号）
    # pirit("") ！错误：注意函数单词拼写
    # print("") √正确

    # （空格）print("") ！错误：意外缩进
    # （顶格）print("") √正确

    # print("123")print("456") ！错误：每个函数单独一行
    # print("123")
    # print("456")
    # √正确

    # print(hello world) ！错误：没有这两个变量，加引号变成字符串
    # print("hello world") √正确

# 【注释】
    # 我是单行注释
'''
    我是多行注释
'''
"""
    我也是多行注释
"""
```

- 关于`print()`函数

```python
# print()函数常见参
print("字符串")
print("多个字符串1","多个字符串2","多个字符串3")
print("多个字符串1","多个字符串2","多个字符串3",sep="|") # 【sep=】 配置输出间隔，默认为空格，！该参数必须在函数最后
print("多个字符串1","多个字符串2","多个字符串3",end="+") # 【end=】 配置结尾，默认值为换行
print("我是不换行的字符串")
```

# 变量

```python
# 变量 variable
# 开辟内存空间，存储数据 【变量名 = 变量值】

liuhaoyu = "牛逼！" #赋值“牛逼！”给变量 liuhaoyu
print(liuhaoyu) #输出liuhaoyu的值

danJia = 20 #设置单价
shuLiang = 10 #设置数量
zongJia = danJia * shuLiang #计算总价
print("总价是",zongJia,sep="")

danJia = 30 #重新赋值变量，同一个变量可以被反复赋值，
zongJia = danJia * shuLiang #这里一定要再次计算，重新赋值
print("经调整后，总价是",zongJia,sep="")
# python运行顺序是从上到下依次运行的，同一变量多次赋值，以最近（最下面）那条的代码为准

#【标识符】
# 标识符：即变量名、函数名。上文liuhaoyu是一个标识符，print()也是一个调用打印函数的标识符
# 标识符不能以数字开头，不能是关键字，可以用下划线，并严格区分大小写，支持中文但不建议使用
# 1liuhaoyu = 1 ！错误，不能以数字开头
# print = 1 ！错误，用了已存在的关键字
# liuhao_yu √可以使用下划线
# LIUHAOYU 不等于 liuhaoyu 严格区分大小写
# 刘浩宇 = "牛逼!" 用中文作标识符（变量名）不会报错，但是不推荐
# (liuhaoyu) = liuhaoyu 可以用括号包，但是不推荐

# 【命名规范】
# 我要求自己使用拼音波峰命名，类首字母大写，其余首字母小写，不同音节首字母大写
# mingZi1 = "刘浩宇"
# mingZi2 = "刘德华"
```

- 数值类型

```python
# 数值类型
# 整型【int】 任意大小的整数
zhengShu1 = 1
print(type(zhengShu1)) #type() 函数：返回值的类型

# 浮点型【float】 小数
xiaoShu1 = 1.1
print(type(xiaoShu1))

# 布尔型【bool】 真True/假False 关键字注意大小写
print(type(True),type(False))
# 布尔值可以当作整形来对待，真1，假0。

print(True + False) # = 1

# 复数型【complex】
fuShu1 = 4 + 7j #j是虚数单位
fuShu2 = 4 + 8j
fuShu = fuShu1 + fuShu1 # 可以进行计算，了解即可
print(type(fuShu))

# 字符串【str】
print(type("我是一个字符串"))
print(type('我是一个字符串')) #单引号也可以
print(type('''三引号也可以''')) #三引号也可以，了解即可
# 建议使用双引号

# 占位符：生成一定格式的字符串
#  【%】
mingZi = "liuhaoyu"
print("我的名字是%s"% mingZi) # %s字符串占位，值等于【%变量名】
nianLing = 30
print("我的年龄是%d"% nianLing) # %d 整数占位
print("我是%s,今年%d"%(mingZi,nianLing)) # 多个占位符语法 %(值1，值2)，且必须对应，字符串对字符串，整数对整数
shuZi1 = 1
print("整数用0补足8位%08d"% shuZi1) # %补位 显示位数 类型，用于填补输出的字符串。%08d意为，用0填充8位输出类型整数，结果是0000001
yuanZhouLv = 3.1415926
print("圆周率是%.2f"% yuanZhouLv) # %.小数点后保留位数，四舍五入，打印出的值为3.14

# 【f】
print(f"f调用变量，花括号读取值。所以我的名字是{mingZi},年龄是{nianLing}") # f{调用变量名}
```

# 输入

```python
# input(提示内容)
shu1 = int(input("请输入数字1"))
shu2 = int(input("请输入数字2"))
# 直接input()读出来的是字符串，所以要用int()定义为整型
shu3 = shu1 + shu2
# 如果没有int()定义为整数，这里+会理解为拼接字符串，结果就是12
print(f"{shu1} + {shu2} =",shu3)

# 【转义字符】
# \t 制表
# \n 换行
# \r 回车，将当前位置移动到本行开头
# \\ 我需要输出"\"，并不需要他转义
# r"字符串"，用r禁止转义，所有的\都是字符串的"\"
```

# 运算符

```python
# 【算数运算符】
# 加减乘除 +-*/
print("1加1等于", 1+1)
print("10减8等于", 10-8)
print("6乘6等于", 6*6)
print("100除10等于", 100/10)
# 除后向下取整 //
print("5除以2向下取整是",5//2)
print("7.0除以2向下取整是",7.0//2) # 若有浮点数值参与运算，结果还是浮点数
# 除后求余 %
print("9除以2求余是", 9%2)
# 去幂 数字**几次方
print("2的三次方是？",2**3)
# 运算优先级 【**】 > 【* / // %】 > 【+ - 】

# 【赋值运算符】
# =
shu1 = 1 #令右边的值等于左边
# += -= *= /= 需要先赋初始值，再进行运算
shu1 += 1 #令右边的值 = 原值 + 1,也可以写作【shu1 = shu1 + 1】
print(shu1) # = 2
shu1 -= 1 #令右边的值 = 原值 - 1，也可以写作【shu1 = shu1 - 1】
print(shu1) # = 1
# 乘除同理

# 【比较运算符】，返回值为布尔类型 True / False
print(1==1) # == ，判断左右两边是否相等
print(1!=1) # != ，判断左右两边是否不等
print(2>1) #> 判断左边是否大于右边
print(2>1) #< 判断左边是否小于右边
print(2>=1) #>= 大于等于
print(2<=1) #<= 小于等于

# 【逻辑运算符】，与或非，返回值为布尔
# and 只有两边都为真，返回真，否则为假
# or 两边任意一边为真，返回真，两边为假，返回假
# not 取相反结果
```

# 判断

```python
# 三元表达式
# if 判断语句 : 为真执行
nianLing = int(input("请输入年龄"))
if nianLing < 18: #如果年龄小于18
    print("你是个未成年") #打印说你是个未成年
else: #否则
    print("你是个成年人") #打印说你是个成年人

# if elif else
if nianLing > 18: #如果年龄大于18
    print("要遵纪守法")
elif nianLing > 14: #如果年龄大于14
    print("但已经可以判刑")
else: #else后面不可以添加任何条件，意为不满足if 和 elif的条件，即执行冒号后的代码
    print("可以为所欲为")

# if嵌套
if nianLing > 0 : #如果这一行条件为假，直接跳转执行else代码
    if nianLing > 18 :#如果上一行为真，那么在判断这一行的条件
        print("你可一定要遵纪守法")
    elif nianLing > 14:
        print("恶魔不分年纪")
    else:
        print("但恶人自有天收")
else:
    print("年龄不合法")
```

# 循环

- while 循环

```python
    i = 1 # 定义计数器
while i <= 5: # 设置循环条件
    print(i) # 当条件为真时，执行命令
    i += 1
'''
while 判断式 :
    判断返回为真即执行命令
'''

# 用while计算等差数列差为1，1+...+100的和
i = 1
he = 0
while i <= 100:
    he += i
    i += 1
print(he)

# 嵌套while循环打印九九乘法表
shu1 = 1 #定义计数器
while shu1 <= 9 : #开始循环从1~9
    shu2 = shu1 #赋值乘数
    while shu2 <=9 : #开始循环从1~9
        print(f"{shu1}x{shu2}={shu1 * shu2}\t",end="")
        shu2 += 1 #计数器
    print("") #换行，等同于"\r"
    shu1 += 1 #计数器
```

- for 循环

```python
'''
for 临时变量 in 可迭代对象:
    执行代码

可迭代对象即要被for遍历的对象
str，字符串对象可以作为可迭代对象
'''
xuanYan = "我刘浩宇最帅" # 新建一个字符串对象
for i in xuanYan: # i为指针便利 xuanYan 变量的值
    print(i) # 读取每个字符的值
    # xuanYan长度是多少，就循环多少次

# for计算 1+...+100的和
he = 0
for i in range(1,101):#range(初始值,结束值,步长默认为1)，range()遵循包前不包后原则，为10才结束，10不打印
    he += i
print(he)
# range(5) 从0开始，步长为1，循环到【<5】即4结束

# 嵌套for循环打印99乘法表
for shu1 in range(1,10):
    for shu2 in range(shu1,10): #这里要用shu1作为初始值
        print(f"{shu1}x{shu2}={shu1 * shu2}\t",end="")
    print("")

# break:某一条件满足时，退出循环
bossXueLiang = 99
while bossXueLiang > 0:
    print(f"boss当前血量为{bossXueLiang}")
    bossXueLiang -= 1
    if bossXueLiang <= 50:
        print("boss逃走了")
        break

# continue:某一条件满足时，跳过本次循环，开始下一次循环
bossXueLiang = 99
while bossXueLiang > 0:
    print(f"boss当前血量为{bossXueLiang}")
    bossXueLiang -= 1
    if bossXueLiang == 50:
        print("boss尝试逃走但失败了")
        continue

# break 和 continue的区别
for a in range(1,5):
    if a == 3:
        # break # break只会到3，即到第三次循环就结束
        continue # continue会跳过3，即跳过第三次循环
    print(a)
```

# 复合数据类型:字符串

```python
# 【字符串编码】
a = "hello"
a = a.encode() #encode()转编码
print(type(a)) #变为bytes，字节类
a = a.decode() #decode()解编码
print(type(a)) #变为str类

# encode,decede函数都可以加参，比如("uft-8")以utf-8规则编解码

# 【字符串运算】
# + 拼接
diYiJu = "黄河之水天上来,"
diErJu = "奔流到海不复回."
yiZhengJu = diYiJu + diErJu
print(yiZhengJu)
# * 重复输出
duLu = "嘟噜~"
print(duLu * 3)
# 成员运算，匹配字符
print("黄" in diYiJu) #黄字在第一句里面，返回True
print("河" not in diErJu) #河字不在第一句里面，返回True

# 【下标】又称索引
yingWenZiMu = "abcdefghijklmnopqrstuvwxyz"
# 这里可以把字符串理解为一个字符组成的数组
print(yingWenZiMu[3]) #应该是d，为什么不是c呢？因为下标从0开始，此处a对应0
# print(yingWenZiMu[26]) #会报错，索引错误，还是因为下标从0开始，26个字母，25就结束
print(yingWenZiMu[-1]) #负数即从右往左数，应该是z，从-1开始，没有-0嘛不是

# 【切片】
print(yingWenZiMu[0:3:1])#[起始位置：结束位置：步长默认为1]
print(yingWenZiMu[-3:])#[起始位置:] 后面不写，表示后面全要
print(yingWenZiMu[:3])#[:结束位置] 前面不写，表示前面全要，但是这里3即下标<3即停止，不会读到d
print(yingWenZiMu[-1::-1])#步长为-1时意味倒着切步长为1，又从-1开始，可以实现字符串内容反转

# 字符串命令格式总结：字符串.调用对象函数(参数)

# 【查找】 对象.find(要匹配字符串,开始下标，结束下标)
print(yingWenZiMu.find("y")) # 在英文字母里找y，返回y这个字母在英文字母里的下标值，如果没有找到，返回-1
print(yingWenZiMu.find("y",0,7)) #在第1个和第8个英文字母里面找y，没有找到，所以返回-1
print(yingWenZiMu.find("y",-6,-2)) #在倒数第六个和倒数第二个之间寻找y，这里还是返回-1，因为包前不包后，-2对应字母y本身

# 【返回索引下标】 对象.index(要匹配的字符串，开始下标，结束下标)
print(yingWenZiMu.index("l")) #l是第12个字母，应该返回11
# print(yingWenZiMu.index("z",0,-1)) #这里包前不包后，所以-1找不到z
# 和find()不同，index如果没找到不会返回值，而是直接报错，

# 【count】 对象.count(要匹配的字符串,开始下标,结束下标)
liuLiuLiu = "666"
print(liuLiuLiu.count('6')) #计算字符6在字符串666中的出现次数，返回3
print(liuLiuLiu.count('7')) #没有就返回0

# 判断是否以某个字符为开头或者结尾，返回值布尔
# startswith
# endswith
print(yingWenZiMu.startswith('a')) #是不是以a为开头？
print(yingWenZiMu.endswith('z')) #是不是以z为结尾？

# 判断是否都是大小写
print(yingWenZiMu.isupper()) #都是大写？
print(yingWenZiMu.islower()) #都是小写？

# replace() 替换
xuanYan = "为人民服务"
print(xuanYan.replace('人民','money'))

# split() 分割
print(yiZhengJu.split(",")) #以，为分割点，分割一整句诗，返回值类型为列表
# 如果字符串中，不包含分割点，就不进行分割

# capitalize()强制首字母大写，同时其他字母小写
abc = "aBc"
print(abc.capitalize()) #返回ABC

# lower()全部小写
# upper()全部大写
print(abc.lower())
print(abc.upper())
```

# 复合数据类型:列表 list

```python
# 列表可以当作数组
# 列表名 = [元素1,元素2,元素3]
# 所有元素在[]内,元素与元素之间用","隔开
# 元素之间数据类型可以各不相同
# 字符串也可以当作一个列表 abc = "abc" = ['a','b','c']
# 所以列表和字符串一样,也是一个可迭代对象

# 定义一个列表
list = ['one','two','three']
# 新增元素
list.append('four') #整体添加,[...,'four'],在末尾添加
list.extend('six') #分散添加,[...,'s','i','x'],只能添加可迭代对象(单个字符不行,字符串可以)
list.insert(4,'five') #指定添加位置整体添加,如果指定的位置有元素,原有元素往后移
print(list)
# 修改元素
list[0] = "yi" #通过下标指定元素,重新赋值即可修改
print(list)
# 查询
print(list[0]) #通过下标指定元素,直接查询
print('two' in list) #判断'two'在不在列表中
print('seven' not in list) #判断'seven'在不在列表中
print(list.index('five')) #返回元素在列表中的下标
print(list.count('two')) #返回元素在列表中的出现次数
# 删除元素
del list[0] #根据下标删除,不带[?]直接删除整个变量
print(list)
list.pop(0) #根据下标删除,()不带参数,默认删除最后一个,下标超出范围,会报错
print(list)
list.remove('three') #根据元素值删除,该方法会遍历对象,找到相等的元素,实现删除,如果没有指定元素,会报错.另外remove只会删除第一个找到的相等元素,然后立刻退出遍历
print(list)

# 排序
numbers = [1,5,3,7]
numbers.sort() # 从小到大排序
print(numbers)
numbers.reverse() #倒叙当前列表
print(numbers) # 先从小到大排序,再倒转,就可以实现从大到小

# 列表推导式 [表达式 for 变量 in 列表 if 条件]
li = []
[li.append(i) for i  in range(1,10) if i % 2 == 1] # 把10以内(range(1,10))的奇数(i % 2 == 1)添加到空列表li中(li.append(i))
print(li)

# 列表嵌套
shiYiNei = [[1,3,5,7,9],[2,4,6,8]] #一个列表里面可以有多个列表[[子列表1],[子列表2],[...]]
print(shiYiNei[0][4]) #如果要指到嵌套列表中的某个元素:列表[子列表于父列表中的下标][元素于子列表中的下标]

# 小应用:模拟注册用户,重复用户名不允许注册
userNames = ['liudehua','liuhaoyu']
while 1:
    newName = input("请输入用户名")
    if newName not in userNames:
        userNames.append(newName)
        print(f"注册成功,欢迎你{newName}")
        break
    else:
        print(f"你输入的用户名{newName}已被注册")
print(f"当前拥有以下用户{userNames}")
```

# 复合数据类型:元组 tuple

```python
# 元组
# 基本格式:元组名 = (元素1,元素2,...)
# 所有元素在小括号里(),用逗号,隔开,可以是不同数据类型
# 和列表不同，元组只支持查询操作,对原始数据的保护更好
tua = (1)
print(type(tua)) #会发现是int类型,当元组内只有一个元素时,元组的数据类型和元素一致
tua =(1,)#所以只有一个元素时,末尾要加逗号
print(type(tua)) #现在就是tuple元组类型了
tua = () #也可以定义空元组
print(type(tua)) #孔院组也是tuple元组类型
tua = (1,3,5,7,9) #元组也有下标，从0开始
# tua[0] = 2 #报错：tuple object doesn't support item assignment 元组对象不支持修改
# 元组的应用场景
# 函数的参数和返回值
# 格式化输出后面的()就是元素
# 保护原始数据安全
name = "liuhaoyu"
age = 30
print("姓名是%s，年龄为%d" %(name,age))
info = (name,age)
print("姓名是%s,年龄是%d" %info)
```

# 复合数据类型:字典 dict

```python
# 字典
# 基本格式： 字典名 = {key:value, key:value}
# 字典于数组，可以把key键当作下标，value值当作下标对应值。
# 键具备为唯一性，值可以重复，当出现重复键名时，值取最后的
# 使用键值对存储描述一个物品的相关信息
dic = {'name':'liuhaoyu','name':'liudehua'} #name为liudehua因为键名重复，取最后的值

# 查找
# print(dic[name]) #直接读不存在的键会报错，找不到name，因为字典里的name是有引号的
print(dic['name']) #直接根据键名指到值
print(dic.get('age')) #如果调用.get()，找不到会返回None
print(dic.get('age',"不存在")) #get(keyName,如果不存在返回的值)

# 修改
dic['name'] = "liuhaoyu"
print(dic['name'])

# 新增
dic['age'] = 30 #当键名不存在时，直接赋值视作新增
print(dic)

# 删除
del dic #直接删除整个字典变量
dic = {'name': 'liuhaoyu', 'age': 30}
del dic['age'] #删除指定键的键值对，如果键名不存在，会报错
print(dic)
dic.clear() #清空dic字典的内容
print(dic) #dic依旧存在，但内容为{空}
#dic.pop(key) #同 del dic[key]
#dic.popitem #删除最后一个键值对

# 求长度
dic = {'name': 'liuhaoyu', 'age': 30}
print(len(dic)) #len()求长度，长度为键值对个数，列表、字符串也可以调用len()函数求长度

# 返回字典里面包含的所有键名
print(dic.keys()) #返回值是一个列表
# 返回字典里面包含的所有值
print(dic.values()) #同样，是个列表
# 返回字典里面包含的全部键值对
print(dic.items()) #返回的是元组
```

# 复合数据类型:集合 set

```python
# 集合
# 格式： 集合名 = {元素1,元素2,元素3,...}

# 字典和集合都是{}？
s1 = {}
print(type(s1)) #读取发现这是个字典类型
s2 = set() #set()才能定义空集合
print(type(s2))

# 集合拥有无序性
s3 = {1,2,3,4,5,6}
s4 = {'a','b','c','d','e','f'}
print(s3) #发现有序
print(s4) #发现无序
# 集合无序实现的方式涉及hash表，字母有随机hash值，但整数就是自己本身
# print(hash('a'))
# print(hash('b'))
# print(hash('c'))
# print(hash(1))
# print(hash(2))
# print(hash(3))

# 集合拥有唯一性
s5 = {1,2,2,3,3,4,4,5,5,6}
print(s5) #集合中的元素唯一，相同元素自动去重

# 添加
s5.add(7) #添加一个整体，添加整数7 .add()只能添加一个元素
# s5.update(8,9) #报错，要求添加可迭代对象
s5.update((8,9)) #这样才能加进去
s5.update('89') #分别添加字符8、9
s5.add(1) #添加已有的元素，不进行任何操作
print(s5)

# 删除
s5.remove('8') #删除与参数值相等的元素，如果没有会报错
print(s5)
s5.pop() #默认删除根据hash表排序后的第一个元素
print(s5)
s5.discard('9') #删除与参数值相等的元素，如果没有，不会报错
print(s5)

# &求交集、|求并集，计算后的返回值就是集合
a = {1,2,3,4}
b = {3,4,5,6}
print(a & b) #3、4重复出现，交集为3、4
print(a | b) #1,2,3,4,5,6 ，3、4只出现一次
```

# 类型转换

```python
# 类型转换
# int() 转整数
x = 1.8
int(x)  #不会改变x的实际值
print(x,type(x))  #所以x还是小数
y = int(x) #用新变量接受该值
print(y,type(y)) #发现新值只保留原值整数部分

# float() 转小数
a = float(1)
print(a,type(a)) #float转整数，自动加一位小数

# str() 转换为字符串
b = str(1.20)
print(b,type(b)) #str转小数，会自动去空位0，除非是1.0，会保留.0

# eval() 执行str里面的运算式
print(10+10)
print('10' + '10') #这里变成字符串拼接1010
print('10 + 10')
print(eval('10 + 10'))
# eval()还可以字符串的实现类型转换
li = eval('[1,2,3]') #只要字符串里面写的对
print(li,type(li)) #相当于去掉引号

# list() 将可迭代对象转为列表 可迭代对象包括：str,tuple,dict,set
print(list('abcd'))
print(list((2,3)))
print(list({1,2,3,4}))
print(list({'name' : 'liuhaoyu','age' : 18})) #字典只保留键名
```

# 深浅拷贝

```python
# 深浅拷贝
# 一、了解赋值的概念
a = 1 #将等号右边的值，赋给等号左边的变量，此时，a就是一个指到特定内存空间的指针，内存空间里面存储的，就是1
print(id(a)) #id() 展示变量在内存中的地址
# 二、了解可变对象、不可变对象：
'''
可变对象的值发生改变时，其内存地址不变，内存空间里存储的内容发生变化，可变对象包括：列表、字典、集合
不可变对象，只能通过重新赋值使其内存空间存储的内容发生变化，而重新赋值也意味着其内存地址改变。
深浅拷贝只针对可变对象。
'''
# 三、深浅拷贝 copy & deep copy
import copy #导入copy模块
li = [1,2,[3,4]]
li1 = li #赋值：当两边都是变量的时候，=等号等效于，让li1和li的内存地址相同，指向同一内存地址
print(id(li))
print(id(li1))
# 所以当我们对li进行任何操作时，li1的值也会发生改变，因为两个变量都指到同一内存
li.append(5)
print(li)
print(li1) #li1会随着li变化而变化
# 但某些情况，我们需要一个变量存储备份原始数据，另一个变量进行操作，这时候就需要使用copy模块了
# 浅拷贝 copy.copy(object)
li2 = copy.copy(li)
print(id(li))
print(id(li2)) #会发现,li和li1的内存地址不同
# 所以我们再次对li进行操作时，li1就不会发生改变了
li.append(6)
print(li)
print(li2) #没有6
# 但是注意，浅拷贝只针对表层数据，嵌套数据的指针还是指向同一内存，li里有子列表[...,[3,4],...]，这就是一个嵌套数据
print(id(li[2])) #[3,4]的下标是2
print(id(li2[2])) #会发现li的子列表和li2的子列表地址是一样的
# 所以，当我们修改li[2]时，li2[2]的值也会发生改变
li[2].append(7)
print(li)
print(li2) # 都变成[...,[3,4,7],...]了
# 当然，我们通常使用浅拷贝就可以完成大部分的工作，但我们实在需要嵌套数据也独立时，就需要使用deepcopy深拷贝实现数据完全独立
# 深拷贝 copy.deepcopy(object)
li3 = copy.deepcopy(li)
print(id(li[2]))
print(id(li3[2])) #会发现嵌套数据的地址也不同了
# 所以，使用深拷贝更占用内存
```

# 函数(又称方法) function

- 1: 函数的定义和三种参数

```python
# 函数，又称方法
'''
定义格式格式：
def 函数名(形参,...):
    函数体

    return 返回值
'''
# 定义一个两数求和的函数,形参为a和b (a,b)
def liangShuQiuHe(a,b):
    return a + b #返回值为a+b
    #函数的返回值有三种类型
    #没有return语句,或者 return为空时,为 None
    #只有一个返回值时,类型于返回值的类型一样,这里是整数
    #可以return v1,v2,... 返回多个值时,类型为元组

#调用函数,传入实参1和2 (1,2)
he = liangShuQiuHe(1,2)
print(he)

# 参数
# 1,必备参数:如上,设置(a,b)两个形参,调用时需要传入(1,2)两个一一对应的值给ab赋值.
# 2,默认参数:即给形参设置默认值,需要注意默认必备参数必须写在默认参数前面.
def qiuMi(x,t=2):#x是乘数,t是乘方,默认t为2,即求平方
    return x**t #**求幂

pingFang = qiuMi(2) #这里指定一个参数2,表示2作为乘数,第二个参数是乘方,不写默认为2
liFang = qiuMi(2,3) #这里t=3,求立方
print("2的平方是",pingFang)
print("2的立方是",liFang)

# 3,可变参数
def qiuHe(*args): #标准写法 (*args)
    he = 0
    for i in args: #可变参数中,args的数值类型是元组,遍历他
        he += i #多数求和
    return he

he = qiuHe(1,2,3,4,5)
print("1,2,3,4,5的和是",he)

# 4,关键字参数
def moNiZhuCe(**kwargs): #模拟注册
    # 此时,**kwargs的值必须是字典
    print("用户名:",kwargs['username'],"注册成功")
    print("再毫不留情地把密码显示在屏幕上:",kwargs['password'])

userInfo = {} #定义空字典
userInfo['username'] = input('请输入用户名') #配置键值对
userInfo['password'] = input('请输入密码')

# 给关键字参数赋值,需要加**
moNiZhuCe(**userInfo)

# 函数嵌套
'''
嵌套调用:
print(type(a)) # 这就是一种简单的嵌套调用,译为:调用type()函数获取a的数值类型,再打印a的数值类型

嵌套定义:
def functionA (): #外函数
    def functionB(): #内函数
        ...

注意函数定义时,不要再内函数里调用外函数,会无限递归
'''
```

- 2: 函数作用域,lambda 函数,Python 内置函数

```python
# 函数2:作用域、lambda匿名函数、内置函数
# 作用域：全局变量global、嵌套内影响外函数的变量nonlocal、以及局部变量
a = 1 #这就是全局变量
def funA() :
    a = 2 #这就是局部变量，只在funA()里生效，函数运行完，这个变量会被自动销毁，释放内存
    print(f"a在funA()函数中的值是{a}")

funA()
print(f"调用funA()后，a在全局，即整个.py文件中的值是{a}")

# 如果我想要在函数内操作全局变量 ：global声明 变量名1,变量名2,...
def funB() :
    global a #先声明，接下来，对a的操作，影响全局
    a = 2
    print(f"a在funB()函数中的值是{a}")
funB()
print(f"调用funB()后，a在全局，即整个.py文件中的值是{a}")

# 如果我想要在内函数中影响外函数的同名变量： nonlocal声明 变量名1,变量名2,...
def funC() :
    a = 2
    print(f"a在funC()函数中的初始值是{a}")
    def funD() :
        a = 3
        print(f"a在funD()函数中的值是{a}")

    funD()
    print(f"调用funD()后，a在funC()这一函数作用域下其实并未改变，现在a={a}")
    def funE() :
        nonlocal a
        a = 4
        print(f"a在funE()函数中的值是{a}")

    funE()
    print(f"调用funE()后，因为该函数在内部声明a为非本地变量，他会影响funC()中a的值得，现在a={a}")

funC()
print(f"但非本地变量只会影响他所属外层函数的变量，不会影响全局变量，现在全局的a还是{a}")

# 匿名函数 lambda ：函数名 = lambda 形参:返回值
# 个人认为这个很鸡肋，了解即可
liangShuQiuhe = lambda a,b:a+b #等效于 def liangShuQiuHe (a,b) : return a+b

print(liangShuQiuhe(1,2))

# 内置函数：python自带的内置函数，下面是一些常用的例子
print("print()也就是一个内置函数")
x = -1
y = -2
x = abs(x) #abs()求绝对值，也就是所谓的去负号
y = abs(y)
print(f"经过abs()去绝对值后，x,y的值分别为{x},{y}")

i = -3
j = -4
zuiXiao = min(i,j,key=abs) #min(参数1,参数2,...,key=规则)返回括号内参数里最小的那个
zuiDa = max(i,j,key=abs) #max()返回括号内参数里最大的那个
print(f"i和j的绝对值相比较，忽略负号，最小值是{zuiXiao}，最大值是{zuiDa}")

def qiuPingFang(x) : #先整个求平方的函数
    return x ** 2

pingFang = map(qiuPingFang,[1,2,3,4,5]) # 使用map(调用函数，传入一个序列作为参数)，用变量pingFang接受。使序列里每一个元素执行一次调用函数
print(type(pingFang)) #发现是map类型数据
pingFang = list(pingFang) #可以转列表
print(pingFang)

list = [1,2,3,4,5] #先整个列表
tua = ('a','b','c','d','e') #再整个元组

newZip = zip(list,tua) #使用zip(序列1,序列2)可以合并序列
print(type(newZip)) #发现是zip类型数据
newDict = dict(newZip) #可以转字典
print(newDict)
```

# 复合数据拆包

```python
# 拆包：提取序列中的元素，分别赋值
list1 = [1,2,3] #列表
tua1 = (4,5,6)  #元组
set1 = {7,8,9}  #集合

a,b,c = list1 #一一对应，提取值
d,e,f = tua1
g,h,i = set1
print(a,b,c,d,e,f,g,h,i)

# j,k,l,m = list1 #list1只有3个元素，用4个变量提取就会报错
j,*k = list1 #那我们可以这样提取：j对应list1的第一个元素，*k提取后面所有的元素
print(j)
print(k) #会发现k还是个数组

# 字典拆包?
dict1 = {'a':1,'b':2,'c':3}
x,y,z = dict1
print(x,y,z) #发现默认提取key
yi,er,san = dict1.values() #使用 字典.values()读取值
print(yi,er,san) #这样就可以提取值了
zhi1,*zhi2 = dict1.values() #试试这样提取
print(zhi1)
print(zhi2) #会发现提取的是个列表
```

# 包和模块

```python
# 包和模块
# 包的本质是一个带__init__.py的文件夹，新建Python Package ：bao
# 关于__init__文件：我们只要在其他python文件中导入包，就会立刻执行__init__.py文件
# 比如

import bao #import 包名：引入包，会自动执行包里的 __init__.py

# 模块
# 模块的本质就是一个.py文件，里面定义了一些变量，一些函数，统称为功能，在./bao/ 下新建一个模块 daYin.py
# daYin.daYinA() #上面引入了bao,为什么这里不能直接调用daYin模块的daYinA功能呢？
# 原来是因为我们写错了，导入包里的模块需要 from 包 import 模块，意味从包里导入指定模块
from bao import daYin # 从bao里导入daYin模块
daYin.daYinA() #执行daYin模块的daYinA()功能

'''
模块分为3种：
内置模块：即标准库
自定义模块：即我们的自己建的Python Package
第三方模块：
    需要我们在cmd里安装【pip install】
    查看我们已经有的模块【pip list】
'''

'''
导模块标准写法：
import 模块1
import 模块2
同时 import 应该出现在代码最上方，先导包，再编程，不要写到一般想起自己需要啥功能，再在当前行导包

建议不要这样写：
import 模块1,模块2

为了节省内存，我们可以导入指定功能：
from 模块 import 功能
当然对于包里的模块，这句话的意思应该是
from 包 import 模块
'''
```

# 异常

```python
# 定义一个错误 Exception('错误提示')
errorA = Exception('这是一个错误')

# 直接调用 raise 会使程序立刻停止，并且红字报错
# raise errorA

'''
try:
    正常的操作
   ......................
except:
    发生异常，执行这块代码
   ......................
else:
    如果没有异常执行这块代码
    .....................
'''
```

# 函数:递归函数

```python
# 递归函数：函数体在函数内部调用自己
# 必须有明确的结束条件，否则会无限递归，报错：超过最大深度
# 每进行更深一层的递归，问题规模相比上次递归都要有所减少
# 相邻两次重复之间有紧密联系

# 使用递归函数实现斐波拉契数列：1,1,2,3,5,8,13,...
def feiBoLaQiShuLie(n) : #n代表数列的位数
    # 明确结束条件
    if n <= 1:
        return n
    return feiBoLaQiShuLie(n-2) + feiBoLaQiShuLie(n-1) #递归调用自己取得前一位的值

# 获取数据
n = int(input("斐波拉契数列：输入你需要计算的位数。"))
li = []  # 空集合用来存斐波拉契数列

# 循环添加
while n > 0 :
    li.insert(0,feiBoLaQiShuLie(n))
    n -= 1

print(f"斐波拉契数列是：{li}")
```

# 闭包

```python
# 闭包
# 1、嵌套函数
# 2、内层使用外层的局部变量
# 3、外层函数的返回值是内层函数的函数名
# 4、每次开启内函数，都在使用同一闭包变量

def outerFunction(a): #外函数
    print(f"外函数的实参是{a}")
    # 嵌套内函数
    def innerFunction(b):
        print(f"内函数的实参是{b}")
        return a + b #内层使用外层的局部变量a

    return innerFunction #外层函数的返回值是内层函数的函数名，不带括号

# 调用：第一步，先用新变量指到外函数内存地址（变量引用外函数的内存地址）
ot = outerFunction(10) #变量ot指到outerFunction()的内存地址，传入实参10
# 调用：第二部，再给内函数传参，这时候就需要 ot引用外函数(这里写内函数的参数)
print(ot(20))
```

# 函数:装饰器

```python
# 装饰器
# 装饰器本质上是一个闭包函数，它可以让其他函数在不需要做任何代码变动的前提下增加额外功能，装饰器的返回值也是一个函数对象。
# 装饰器需要满足：
# 1、不改变原函数的代码
# 2、不改变原函数的调用方法
# @装饰器名称 <不带括号，换行>

#假设需求：模拟直播打赏功能，打赏前验证登录
#设置外函数，也就是装饰器
def zhiBo(fn): #装饰器，要求接受一个函数作为参数
    def gongNeng(money): #定义一个"包装"
        print("（假设）登录校验成功") #模拟登录验证功能
        fn(money) #包装内调用装饰器接受的函数
    return gongNeng #闭包函数要求外函数的返回值是内函数的名称，这里的意思是，最后zhiBo()返回的执行一次gongNeng()函数

@zhiBo #引用装饰器
def daShang(money): #这里的意思就是调用daShang()前，会让daShang()作为参数传入@zhiBo函数中
    print(f"成功打赏{money}元")

money = float(input('请输入打赏数额'))
daShang(money) #这里的money是值是用于 zhiBo/gongNeng 里的实参

#自己的话总结，装饰器最主要的作用，是为了不修改原函数的代码，而是通过@引用原函数(装饰器)后，在下面编写新功能，然后再把新功能作为参数传入原函数执行

# 被多个装饰器装饰会发生什么?
def funA(fn):
    def inner():
        return "(" + fn() + ")"
    return inner

def funB(fn):
    def inner():
        return "[" + fn() + "]"
    return inner
@funA
@funB
def jiangHua():
    return "哈哈哈"

print(jiangHua()) #当一个函数被多个装饰器装饰时,他会先进离自己最近的装饰器,执行结果再作为参数进更上面的装饰器.
# "哈哈哈" -> 进funB() -> "[哈哈哈]" -> "[哈哈哈]" 作为结果进 funA() -> "([哈哈哈])"
```
