---

title: 学习一下python
slug: python_learning
date: 2021-06-18 13:00:00 UTC+08:00
tags: 
category: 学习
author: yilanxuan
has_math: true

---

主要是不大用，所以存一下。


<!-- TEASER_END:  -->

---

## 基本数据类型

不可变数据（3 个）：Number（数字）、String（字符串）、Tuple（元组）；

可变数据（3 个）：List（列表）、Dictionary（字典）、Set（集合）。

### Number

并没有什么特别的，除开自带高精度

我也不知道我会用哪些

#### 数学函数

|函数	|返回值 ( 描述 )|
| :-----| :---- |
|ceil(x)|	返回数字的上入整数，如math.ceil(4.1) 返回 5|
|floor(x)|	返回数字的下舍整数，如math.floor(4.9)返回 4|
|log(x)	|如math.log(math.e)返回1.0,math.log(100,10)返回2.0|
|log10(x)|	返回以10为基数的x的对数，如math.log10(100)返回 2.0|
|max(x1, x2,...)|	返回给定参数的最大值，参数可以为序列。|
|min(x1, x2,...)|	返回给定参数的最小值，参数可以为序列。|
|modf(x)|	返回x的整数部分与小数部分，两部分的数值符号与x相同，整数部分以浮点型表示。|
|pow(x, y)	|x**y 运算后的值。|
|round(x [,n])|返回浮点数 x 的四舍五入值，如给出 n 值，则代表舍入到小数点后的位数。|
|sqrt(x)	|返回数字x的平方根。|

#### 随机数函数

随机数可以用于数学，游戏，安全等领域中，还经常被嵌入到算法中，用以提高算法效率，并提高程序的安全性。

Python包含以下常用随机数函数：

|函数	|描述|
| :-----| :---- |
|choice(seq)	|从序列的元素中随机挑选一个元素，比如random.choice(range(10))，从0到9中随机挑选一个整数。|
|randrange([start,] stop [,step])	|从指定范围内，按指定基数递增的集合中获取一个随机数，基数默认值为 1|
|random()	|随机生成下一个实数，它在[0,1)范围内。|
|seed([x])	|改变随机数生成器的种子seed。如果你不了解其原理，你不必特别去设定seed，Python会帮你选择seed。|
|shuffle(lst)	|将序列的所有元素随机排序。|
|uniform(x, y)	|随机生成下一个实数，它在[x,y]范围内。|

#### 三角函数

Python包括以下三角函数：

|函数	|描述|
| :-----| :---- |
|acos(x)	|返回x的反余弦弧度值。|
|asin(x)	|返回x的反正弦弧度值。|
|atan(x)	|返回x的反正切弧度值。|
|atan2(y, x)	|返回给定的 X 及 Y 坐标值的反正切值。|
|cos(x)	|返回x的弧度的余弦值。|
|hypot(x, y)	|返回欧几里德范数 sqrt(x\*x + y\*y)。|
|sin(x)	|返回的x弧度的正弦值。|
|tan(x)	|返回x弧度的正切值。|
|degrees(x)	|将弧度转换为角度,如degrees(math.pi/2) ， 返回90.0|
|radians(x)	|将角度转换为弧度|

#### 数学常量

|常量	|描述|
| :-----| :---- |
|pi	|数学常量 pi（圆周率，一般以π来表示）|
|e	|数学常量 e，e即自然常数（自然常数）。|

### String

```py
str = 'lkakioi'

print (str)          # 输出字符串
print (str[0:-1])    # 输出第一个到倒数第二个的所有字符
print (str[0])       # 输出字符串第一个字符
print (str[2:5])     # 输出从第三个开始到第五个的字符
print (str[2:])      # 输出从第三个开始的后的所有字符
print (str * 2)      # 输出字符串两次，也可以写成 print (2 * str)
print (str + "TEST") # 连接字符串
```

1. 反斜杠可以用来转义，使用r可以让反斜杠不发生转义。
2. 字符串可以用+运算符连接在一起，用*运算符重复。
3. ython中的字符串有两种索引方式，从左往右以0开始，从右往左以-1开始。
4. Python中的字符串不能改变。

#### 多行字符串    

```py
s = """
lk ak ioi
[\t]
lktql\nqwq
"""
print(s)
```

#### 字符串格式化

```
a = 'lk'
print ("我叫 %s 今年 %d 岁!" % (a, 10)
```

咕咕咕

#### f-string

```py
>>> name = 'World'
>>> f'Hello {name}'  # 替换变量
'Hello World'
```

#### 运算符

下表实例变量 a 值为字符串 "Hello"，b 变量值为 "Python"：

|操作符	|描述	|实例|
|----|----|-----|
|[ : ]|	截取字符串中的一部分，遵循左闭右开原则，str[0:2] 是不包含第 3 个字符的。|a[1:4] 输出结果 ell|
|in	|成员运算符 - 如果字符串中包含给定的字符返回 True	|'H' in a 输出结果 True|
|not in	|成员运算符 - 如果字符串中不包含给定的字符返回 True	|'M' not in a 输出结果 True|
|r/R|	原始字符串 - 原始字符串：所有的字符串都是直接按照字面的意思来使用，没有转义特殊或不能打印的字符。 原始字符串除在字符串的第一个引号前加上字母 r（可以大小写）以外，与普通字符串有着几乎完全相同的语法。	|```print( r'\n' ) print( R'\n' )```|

#### Python 的字符串内建函数

Python 的字符串常用内建函数如下：

```
1	capitalize()
将字符串的第一个字符转换为大写
2	center(width, fillchar)
返回一个指定的宽度 width 居中的字符串，fillchar 为填充的字符，默认为空格。
3	
count(str, beg= 0,end=len(string))
返回 str 在 string 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 str 出现的次数
4	
bytes.decode(encoding="utf-8", errors="strict")
Python3 中没有 decode 方法，但我们可以使用 bytes 对象的 decode() 方法来解码给定的 bytes 对象，这个 bytes 对象可以由 str.encode() 来编码返回。
5	
encode(encoding='UTF-8',errors='strict')
以 encoding 指定的编码格式编码字符串，如果出错默认报一个ValueError 的异常，除非 errors 指定的是'ignore'或者'replace'
6	
endswith(suffix, beg=0, end=len(string))
检查字符串是否以 obj 结束，如果beg 或者 end 指定则检查指定的范围内是否以 obj 结束，如果是，返回 True,否则返回 False.
7	
expandtabs(tabsize=8)
把字符串 string 中的 tab 符号转为空格，tab 符号默认的空格数是 8 。
8	
find(str, beg=0, end=len(string))
检测 str 是否包含在字符串中，如果指定范围 beg 和 end ，则检查是否包含在指定范围内，如果包含返回开始的索引值，否则返回-1
9	
index(str, beg=0, end=len(string))
跟find()方法一样，只不过如果str不在字符串中会报一个异常。
10	
isalnum()
如果字符串至少有一个字符并且所有字符都是字母或数字则返 回 True，否则返回 False
11	
isalpha()
如果字符串至少有一个字符并且所有字符都是字母或中文字则返回 True, 否则返回 False
12	
isdigit()
如果字符串只包含数字则返回 True 否则返回 False..
13	
islower()
如果字符串中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是小写，则返回 True，否则返回 False
14	
isnumeric()
如果字符串中只包含数字字符，则返回 True，否则返回 False
15	
isspace()
如果字符串中只包含空白，则返回 True，否则返回 False.
16	
istitle()
如果字符串是标题化的(见 title())则返回 True，否则返回 False
17	
isupper()
如果字符串中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是大写，则返回 True，否则返回 False
18	
join(seq)
以指定字符串作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串
19	
len(string)
返回字符串长度
20	
ljust(width[, fillchar])
返回一个原字符串左对齐,并使用 fillchar 填充至长度 width 的新字符串，fillchar 默认为空格。
21	
lower()
转换字符串中所有大写字符为小写.
22	
lstrip()
截掉字符串左边的空格或指定字符。
23	
maketrans()
创建字符映射的转换表，对于接受两个参数的最简单的调用方式，第一个参数是字符串，表示需要转换的字符，第二个参数也是字符串表示转换的目标。
24	
max(str)
返回字符串 str 中最大的字母。
25	
min(str)
返回字符串 str 中最小的字母。
26	
replace(old, new [, max])
把 将字符串中的 old 替换成 new,如果 max 指定，则替换不超过 max 次。
27	
rfind(str, beg=0,end=len(string))
类似于 find()函数，不过是从右边开始查找.
28	
rindex( str, beg=0, end=len(string))
类似于 index()，不过是从右边开始.
29	
rjust(width,[, fillchar])
返回一个原字符串右对齐,并使用fillchar(默认空格）填充至长度 width 的新字符串
30	
rstrip()
删除字符串末尾的空格或指定字符。
31	
split(str="", num=string.count(str))
以 str 为分隔符截取字符串，如果 num 有指定值，则仅截取 num+1 个子字符串
32	
splitlines([keepends])
按照行('\r', '\r\n', \n')分隔，返回一个包含各行作为元素的列表，如果参数 keepends 为 False，不包含换行符，如果为 True，则保留换行符。
33	
startswith(substr, beg=0,end=len(string))
检查字符串是否是以指定子字符串 substr 开头，是则返回 True，否则返回 False。如果beg 和 end 指定值，则在指定范围内检查。
34	
strip([chars])
在字符串上执行 lstrip()和 rstrip()
35	
swapcase()
将字符串中大写转换为小写，小写转换为大写
36	
title()
返回"标题化"的字符串,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle())
37	
translate(table, deletechars="")
根据 str 给出的表(包含 256 个字符)转换 string 的字符, 要过滤掉的字符放到 deletechars 参数中
38	
upper()
转换字符串中的小写字母为大写
39	
zfill (width)
返回长度为 width 的字符串，原字符串右对齐，前面填充0
40	
isdecimal()
检查字符串是否只包含十进制字符，如果是返回 true，否则返回 false。
```


### Tuple

```py
#!/usr/bin/python3

tuple = ( 'abcd', 786 , 2.23, 'lkakioi', 70.2  )
tinytuple = (123, 'lkakioi')

print (tuple)             # 输出完整元组
print (tuple[0])          # 输出元组的第一个元素
print (tuple[1:3])        # 输出从第二个元素开始到第三个元素
print (tuple[2:])         # 输出从第三个元素开始的所有元素
print (tinytuple * 2)     # 输出两次元组
print (tuple + tinytuple) # 连接元组
```

### List

```py
list = [ 'abcd', 786 , 2.23, 'qwq', 70.2 ]
tinylist = [123, 'qwq']
print(list)           # 输出完整列表
print(list[0])        # 输出列表第一个元素
print(list[1: 3])     # 从第二个开始输出到第三个元素
print(list[2: ])      # 输出从第三个元素开始的所有元素
print(list * 2)       # 输出两次列表
print(list + tinylist)# 连接列表

a = [1, 2, 3]
print(a)
a[1] = 1000
print(a)               # 列表中的元素是可以改变
list.append('lkakioi') # 添加列表项
del list[2]            # 删除第二个元素
```

使用嵌套列表即在列表里创建其它列表，例如：

```py
>>>a = ['a', 'b', 'c']
>>> n = [1, 2, 3]
>>> x = [a, n]
>>> x
[['a', 'b', 'c'], [1, 2, 3]]
```

1. len(list) 列表元素个数
2. max(list) 返回列表元素最大值
3. min(list) 返回列表元素最小值
4. list(seq) 将元组转换为列表

```py
1. list.append(obj) 在列表末尾添加新的对象
2. list.count(obj) 统计某个元素在列表中出现的次数
3. list.extend(seq) 在列表末尾一次性追加另一个序列中的多个值（用新列表扩展原来的列表）
4. list.index(obj) 从列表中找出某个值第一个匹配项的索引位置
5. list.insert(index, obj) 将对象插入列表
6. list.pop([index=-1]) 移除列表中的一个元素（默认最后一个元素），并且返回该元素的值
7. list.remove(obj) 移除列表中某个值的第一个匹配项
8. list.reverse() 反向列表中元素
9. list.sort( key=None, reverse=False) 对原列表进行排序
    key -- 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
reverse -- 排序规则，reverse = True 降序， reverse = False 升序（默认）。
10. list.clear() 清空列表
11. list.copy() 复制列表
```

```py
# 获取列表的第二个元素
def takeSecond(elem):
    return elem[1]
random = [(2, 2), (3, 4), (4, 1), (1, 3)]
# 指定第二个元素排序
random.sort(key=takeSecond)
# 输出类别
print ('排序列表：', random)

# 排序列表：[(4, 1), (2, 2), (1, 3), (3, 4)]
```

### Dictionary

```py
dict = {}
dict['one'] = "lkakioi"
dict[2]     = "lkakl"

tinydict = {'lkk': 'lktql','hh':1, 'lk': 'lktqltql'}

print (dict['one'])       # 输出键为 'one' 的值
print (dict[2])           # 输出键为 2 的值
print (tinydict)          # 输出完整的字典
print (tinydict.keys())   # 输出所有键
print (tinydict.values()) # 输出所有值

# 几种形式
tinydict = {'name': 'lktql','code':1, 'site': 'lktqltql'}
a = dict([('lktql', 1), ('lktqqql', 2), ('lktqqqqqqqqqqqqql', 3)])
b = {x: x**2 for x in (2, 4, 6)}
c = dict(lkakioi = 1, lkakl = 2, lktql = 3)
print(a, b, c)

# 然后可以修改
a['lktql'] = 4
```
```py
字典内置函数&方法
Python字典包含了以下内置函数：

序号	函数及描述	实例
1. len(dict)
计算字典元素个数，即键的总数。	
>>> dict = {'Name': 'Runoob', 'Age': 7, 'Class': 'First'}
>>> len(dict)
3

2.str(dict)
输出字典，以可打印的字符串表示。	
>>> dict = {'Name': 'Runoob', 'Age': 7, 'Class': 'First'}
>>> str(dict)
"{'Name': 'Runoob', 'Class': 'First', 'Age': 7}"

3. type(variable)
返回输入的变量类型，如果变量是字典就返回字典类型。	
>>> dict = {'Name': 'Runoob', 'Age': 7, 'Class': 'First'}
>>> type(dict)
<class 'dict'>
```

Python字典包含了以下内置方法：
```py
1	radiansdict.clear() 删除字典内所有元素
2	radiansdict.copy() 返回一个字典的浅复制
3	dict.fromkeys(seq[, value]) 创建一个新字典，以序列seq中元素做字典的键，val为字典所有键对应的初始值

seq = ('name', 'age', 'sex')
dict = dict.fromkeys(seq)
print ("新的字典为 : %s" %  str(dict))
dict = dict.fromkeys(seq, 10)
print ("新的字典为 : %s" %  str(dict))

# 新的字典为 : {'age': None, 'name': None, 'sex': None}
# 新的字典为 : {'age': 10, 'name': 10, 'sex': 10}

# 剩下的查表去
```

### Set

满足所有数学Set的特性。

创建一个空的集合必须使用 `set()`

```py
>>> a = set('abracadabra')
>>> b = set('alacazam')
>>> a                                  
{'a', 'r', 'b', 'c', 'd'}
>>> a - b                              # 集合a中包含而集合b中不包含的元素
{'r', 'd', 'b'}
>>> a | b                              # 集合a或b中包含的所有元素
{'a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'}
>>> a & b                              # 集合a和b中都包含了的元素
{'a', 'c'}
>>> a ^ b                              # 不同时包含于a和b的元素
{'r', 'd', 'b', 'm', 'z', 'l'}
```

```
1. 添加 `s.add( x )`
还有一个方法，也可以添加元素，且参数可以是列表，元组，字典等，语法格式如下：
`s.update( x )`

2. 移除 `s.remove( x )`

我们也可以设置随机删除集合中的一个元素，语法格式如下：`s.pop()`

其实也不是随机，但是确实没有去研究过python的集合是怎么存的

3. `len(s)`

4. `s.clear()`
```


剩下的查表去

## 运算符

其实我习惯不管优先级的，反正用起来打括号就好

>is   is not	身份运算符
>in   not in	成员运算符
>not  and or	逻辑运算符 优先级：not and or


```py
a, b = 0, 1
while b < 1000:
    print(b, end=',')
    a, b = b, a+b
```

```py
a = int(input())
if a <= 0:
    print("lktql")
elif a <= 10:
    print("lkakl")
else:
    if a % 2 == 0: 
        print("lkakioi")
    else:
        print("lktxdy") 
```

```py
for letter in range(10, 0, -1):     # 第一个实例
   print (letter, end = " ")

# 输出的是 10 9 8 7 6 5 4 3 2 1
```

```py
for i in range(5):
    print(i)

0
1
2
3
4
```

```py
list=[1,2,3,4]
it = iter(list)    # 创建迭代器对象
for x in it:
    print (x, end=" ")
```

```py
#!/usr/bin/python3 
import sys         # 引入 sys 模块
list=[1,2,3,4]
it = iter(list)    # 创建迭代器对象
 
while True:
    try:
        print (next(it))
    except StopIteration:
        sys.exit()
```

```py
class MyNumbers:
  def __iter__(self):
    self.a = 1
    return self
 
  def __next__(self):
    x = self.a
    self.a += 1
    return x
 
myclass = MyNumbers()
myiter = iter(myclass)
 
print(next(myiter))
print(next(myiter))
print(next(myiter))
print(next(myiter))
print(next(myiter))
```
执行结果
1
2
3
4
5