# Python 笔记

## 快速使用 python 启动 HTTP server

* python 2.x

  `python –m SimpleHTTPServer`

* python 3.x

  `python –m http.server`

## Python 技巧示例

### Python title()方法

Python title() 方法返回"标题化"的字符串,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle())。

title()方法语法：

```python
str.title()
```

实例:

```python
#!/usr/bin/python

str = "this is string example....wow!!!";
print str.title();
```

> This Is String Example....Wow!!!

### 交换赋值

```python
##不推荐
temp = a
a = b
b = a

##推荐
a, b = b, a  #  先生成一个元组(tuple)对象，然后unpack
```

### Unpacking

```python
##不推荐
l = ['David', 'Pythonista', '+1-514-555-1234']
first_name = l[0]
last_name = l[1]
phone_number = l[2]

##推荐
l = ['David', 'Pythonista', '+1-514-555-1234']
first_name, last_name, phone_number = l
# Python 3 Only
first, *middle, last = another_list
```

### 使用操作符 in

```python
##不推荐
if fruit == "apple" or fruit == "orange" or fruit == "berry":
    # 多次判断

##推荐
if fruit in ["apple", "orange", "berry"]:
    # 使用 in 更加简洁
```

### 字符串操作

```python
##不推荐
colors = ['red', 'blue', 'green', 'yellow']

result = ''
for s in colors:
    result += s  #  每次赋值都丢弃以前的字符串对象, 生成一个新对象

##推荐
colors = ['red', 'blue', 'green', 'yellow']
result = ''.join(colors)  #  没有额外的内存分配
```

### 字典键值列表

```python
##不推荐
for key in my_dict.keys():
    #  my_dict[key] ...

##推荐
for key in my_dict:
    #  my_dict[key] ...

# 只有当循环中需要更改key值的情况下，我们需要使用 my_dict.keys()
# 生成静态的键值列表。
```

### 字典键值判断

```python
##不推荐
if my_dict.has_key(key):
    # ...do something with d[key]

##推荐
if key in my_dict:
    # ...do something with d[key]
```

### 字典 get 和 setdefault 方法

```python
##不推荐
navs = {}
for (portfolio, equity, position) in data:
    if portfolio not in navs:
            navs[portfolio] = 0
    navs[portfolio] += position * prices[equity]
##推荐
navs = {}
for (portfolio, equity, position) in data:
    # 使用 get 方法
    navs[portfolio] = navs.get(portfolio, 0) + position * prices[equity]
    # 或者使用 setdefault 方法
    navs.setdefault(portfolio, 0)
    navs[portfolio] += position * prices[equity]
```

### 判断真伪

```python
##不推荐
if x == True:
    # ....
if len(items) != 0:
    # ...
if items != []:
    # ...

##推荐
if x:
    # ....
if items:
    # ...
```

### 遍历列表以及索引

```python
##不推荐
items = 'zero one two three'.split()
# method 1
i = 0
for item in items:
    print i, item
    i += 1
# method 2
for i in range(len(items)):
    print i, items[i]

##推荐
items = 'zero one two three'.split()
for i, item in enumerate(items):
    print i, item
```

### 列表推导

```python
##不推荐
new_list = []
for item in a_list:
    if condition(item):
        new_list.append(fn(item))

##推荐
new_list = [fn(item) for item in a_list if condition(item)]
```

### 列表推导-嵌套

```python
##不推荐
for sub_list in nested_list:
    if list_condition(sub_list):
        for item in sub_list:
            if item_condition(item):
                # do something...
##推荐
gen = (item for sl in nested_list if list_condition(sl) \
            for item in sl if item_condition(item))
for item in gen:
    # do something...
```

### 循环嵌套

```python
##不推荐
for x in x_list:
    for y in y_list:
        for z in z_list:
            # do something for x &amp;amp; y

##推荐
from itertools import product
for x, y, z in product(x_list, y_list, z_list):
    # do something for x, y, z
```

### 尽量使用生成器代替列表

```python
##不推荐
def my_range(n):
    i = 0
    result = []
    while i &amp;lt; n:
        result.append(fn(i))
        i += 1
    return result  #  返回列表

##推荐
def my_range(n):
    i = 0
    result = []
    while i &amp;lt; n:
        yield fn(i)  #  使用生成器代替列表
        i += 1
*尽量用生成器代替列表，除非必须用到列表特有的函数。
```

### 中间结果尽量使用 imap/ifilter 代替 map/filter

```python
##不推荐
reduce(rf, filter(ff, map(mf, a_list)))

##推荐
from itertools import ifilter, imap
reduce(rf, ifilter(ff, imap(mf, a_list)))
*lazy evaluation 会带来更高的内存使用效率，特别是当处理大数据操作的时候。
```

### 使用 any/all 函数

```python
##不推荐
found = False
for item in a_list:
    if condition(item):
        found = True
        break
if found:
    # do something if found...

##推荐
if any(condition(item) for item in a_list):
    # do something if found...
```

### 属性(property)

```python
##不推荐
class Clock(object):
    def __init__(self):
        self.__hour = 1
    def setHour(self, hour):
        if 25 &amp;gt; hour &amp;gt; 0: self.__hour = hour
        else: raise BadHourException
    def getHour(self):
        return self.__hour

##推荐
class Clock(object):
    def __init__(self):
        self.__hour = 1
    def __setHour(self, hour):
        if 25 &amp;gt; hour &amp;gt; 0: self.__hour = hour
        else: raise BadHourException
    def __getHour(self):
        return self.__hour
    hour = property(__getHour, __setHour)
```

### 使用 with 处理文件打开

```python
##不推荐
f = open("some_file.txt")
try:
    data = f.read()
    # 其他文件操作..
finally:
    f.close()

##推荐
with open("some_file.txt") as f:
    data = f.read()
    # 其他文件操作...
```

### 使用 with 忽视异常(仅限 Python 3)

```python
##不推荐
try:
    os.remove("somefile.txt")
except OSError:
    pass

##推荐
from contextlib import ignored  # Python 3 only

with ignored(OSError):
    os.remove("somefile.txt")
```

### 使用 with 处理加锁

```python
##不推荐
import threading
lock = threading.Lock()

lock.acquire()
ry:
    # 互斥操作...
finally:
    lock.release()

##推荐
import threading
lock = threading.Lock()

with lock:
    # 互斥操作...
```

### 表达式

```python
# 快速构成一个字典
print dict(zip(('abcd',range(4)))

# 用类似3目运算输出
print 'ok' if a==1 else 'ko'

# 直接 return 条件判断
def test(m)
   return 'a' if m == 1 else 'b'

# 推导列表生成字典
list1 = ((1, 'a'), (1, 'b'))
print {x[0]:x[1] for x in list1}
print {x:y for x in range(4) for y in range(10,14)}
```

### 计算 x 的 n 次方

```python
def power(x,n)
  s = 1
  while n > 0:
    n = n -1
    s = s * x
    return s
```

### 计算 a*a + b*b + c\*c +

```python
def clac(*numbers):
  sum = 0
  for n in numbers:
    sum = sum + n * n
    return sum
```

### 列出当前目录下所有文件和目录名

```python
[d for d in os.listdir('.')]
```

### 将 list 中所有的字符转换成小写

```python
L = ['Hello', 'World', 'IBM', 'Apple']
[s.lower() for s in L]
```

### 列出某个路径下的所有文件和文件夹的路径

```python
def print_dir():
  filepath = input("Please key in a path: ")
  if filepath =="":
    print("please key in current path!")
  else:
    for i in os.listdir(filepath):
      print(os.path.join(filepath,i))

print(print_dir())
```

### 替换列表中的 3 为 3a

```python
num = [1,2,3,4,5,6,7,8,9,2,1,4,5,3]

# print(num.count(3))
# print(num.index(3))

for i in range(num.count(3)):
  ele_index = num.index(3)
  num[ele_index]="3a"
  print(num)
```

### 打印列表中的所有项目

```python
L = ["aa", "bb", "cc"]
for i in range(len(L)):
  print('List Has %s'%L[i])
```

### 列表合并去重

```python
list1 = [11, 22 ,33, 44, 55]
list2 = [44, 55, 66, 77, 88]

list3 = list1 + list2
print list3
#print(set(list3))
print(list(set(list3)))
```

### 进制转换

```python
dec = int(input("Please key in a number: "))

print("十进制数为: ", dec)
print("转换为二进制为: ", bin(dec))
print("转换为八进制为: ", oct(dec))
print("转换为十六进制为: ", hex(dec))
```

### Python Subprocess

```python
class EMMC_VENDOR_HWCHECK(unittest.TestCase):

  def runTest(self):

  device_data = shopfloor.GetDeviceData()
  shopfloor_emmc_vendor=device_data['eMMC_Vendor']

  logging.info('shopfloor eMMC vendor : %s', shopfloor_emmc_vendor)

  if shopfloor_emmc_vendor == 'Hynix' :
    cmdline="gooftool probe --no_ic --no_vol --comps storage"
    process = subprocess.Popen(
    cmdline, shell=True,
        stdout=subprocess.PIPE, stderr=subprocess.STDOUT)
    stdout, _ = process.communicate()
    logging.info('Script output: %s', stdout)
    logging.info('Script return code: %s', process.returncode)
    if process.returncode != 0:
        raise error.TestError('Script failed with return code %s' %
                              process.returncode)

    tmp=stdout.find("manfid: '0x000090'")   #check Hynix Vendor
         if tmp < 0:
      raise IOError('shopfloor eMMC is', shopfloor_emmc_vendor, 'but Machine is not Hynix eMMC')
```

## Python 字符串拼接示例

### 来自 C 语言的%方式

```python
print('%s %s' % ('Hello', 'world'))
>>> Hello world
```

%号格式化字符串的方式继承自古老的 C 语言，这在很多编程语言都有类似的实现。上例的%s 是一个占位符，它仅代表一段字符串，并不是拼接的实际内容。实际的拼接内容在一个单独的%号后面，放在一个元组里。

类似的占位符还有：%d（代表一个整数）、%f（代表一个浮点数）、%x（代表一个 16 进制数），等等。
%占位符既是这种拼接方式的特点，同时也是其限制，因为每种占位符都有特定意义，实际使用起来太麻烦了。

### format()拼接方式

```python
# 简洁版
s1 = 'Hello {}! My name is {}.'.format('World', 'Python猫')
print(s1)
>>>Hello World! My name is Python猫.

# 对号入座版
s2 = 'Hello {0}! My name is {1}.'.format('World', 'Python猫')
s3 = 'Hello {name1}! My name is {name2}.'.format(name1='World', name2='Python猫')
print(s2)
>>>Hello World! My name is Python猫.
print(s3)
>>>Hello World! My name is Python猫.
```

这种方式使用花括号{}做占位符，在 format 方法中再转入实际的拼接值。容易看出，它实际上是对%号拼接方式的改进。这种方式在 Python2.6 中开始引入。

上例中，简洁版的花括号中无内容，缺点是容易弄错次序。对号入座版主要有两种，一种传入序列号，一种则使用 key-value 的方式。实战中，我们更推荐后一种，既不会数错次序，又更直观可读。

### () 类似元组方式

```python
s_tuple = ('Hello', ' ', 'world')
s_like_tuple = ('Hello' ' ' 'world')

print(s_tuple)
>>>('Hello', ' ', 'world')
print(s_like_tuple)
>>>Hello world

type(s_like_tuple) >>>str
```

注意，上例中 s_like_tuple 并不是一个元组，因为元素间没有逗号分隔符，这些元素间可以用空格间隔，也可以不要空格。使用 type()查看，发现它就是一个 str 类型。我没查到这是啥原因，猜测或许()括号中的内容是被 Python 优化处理了。

这种方式看起来很快捷，但是，括号()内要求元素是真实字符串，不能混用变量，所以不够灵活。

```python
# 多元素时，不支持有变量
str_1 = 'Hello'
str_2 = (str_1 'world')
>>> SyntaxError: invalid syntax
str_3 = (str_1 str_1)
>>> SyntaxError: invalid syntax
# 但是下面写法不会报错
str_4 = (str_1)
```

### 面向对象模板拼接

```python
from string import Template
s = Template('${s1} ${s2}!')
print(s.safe_substitute(s1='Hello',s2='world'))
>>> Hello world!
```

### 常用的+号方式

```python
str_1 = 'Hello world！ '
str_2 = 'My name is Python猫.'
print(str_1 + str_2)
>>>Hello world！ My name is Python猫.
print(str_1)
>>>Hello world！
```

### join()拼接方式

```python
str_list = ['Hello', 'world']
str_join1 = ' '.join(str_list)
str_join2 = '-'.join(str_list)
print(str_join1) >>>Hello world
print(str_join2) >>>Hello-world
```

str 对象自带的 join()方法，接受一个序列参数，可以实现拼接。拼接时，元素若不是字符串，需要先转换一下。可以看出，这种方法比较适用于连接序列对象中（例如列表）的元素，并设置统一的间隔符。

当拼接长度超过 20 时，这种方式基本上是首选。不过，它的缺点就是，不适合进行零散片段的、不处于序列集合的元素拼接。

### f-string 方式

```python
name = 'world'
myname = 'python_cat'
words = f'Hello {name}. My name is {myname}.'
print(words)
>>> Hello world. My name is python_cat.
```

f-string 方式出自 PEP 498（Literal String Interpolation，字面字符串插值），从 Python3.6 版本引入。其特点是在字符串前加 f 标识，字符串中间则用花括号{}包裹其它字符串变量。

这种方式在可读性上秒杀 format()方式，处理长字符串的拼接时，速度与 join()方法相当。

尽管如此，这种方式与其它某些编程语言相比，还是欠优雅，因为它引入了一个 f 标识。而其它某些程序语言可以更简练，比如 shell：

```bash
name="world"
myname="python_cat"
words="Hello ${name}. My name is ${myname}."
echo $words
>>>Hello world. My name is python_cat.
```

总结一下，我们前面说的“字符串拼接”，其实是从结果上理解。若从实现原理上划分的话，我们可以将这些方法划分出三种类型：

格式化类：`%、format()、template`
拼接类：`+、()、join()`
插值类：`f-string`
当要处理字符串列表等序列结构时，采用 join()方式；拼接长度不超过 20 时，选用+号操作符方式；长度超过 20 的情况，高版本选用 f-string，低版本时看情况使用 format()或 join()方式。

## python 输出 shell 命令执行结果

```python
import os,subprocess
p = subprocess.Popen("df -h", shell=True, stdout=subprocess.PIPE)
out = p.stdout.readlines()

for line in out:
    print line.strip()
　　将df -h的执行结果输出到stdout,再用readlines方法读出来，再print出来


import os,subprocess
x = subprocess.check_output(["date", "+%T"])
print x
　　这个方法可以将某个命令的结果输出到python
```

```python
  def RunCommand(self, cmd):
    # unused

    result_str = ''
    process = subprocess.Popen(cmd, shell=True, stdout=subprocess.PIPE,
                               stderr=subprocess.PIPE)
    result_f = process.stdout
    error_f = process.stderr
    errors = error_f.read()
    if errors: pass
    result_str = result_f.read().strip()
    if result_f:
      result_f.close()
    if error_f:
      error_f.close()
    return result_str

  def RunCommandToFile(self, cmd):
    # unused

    fdout = open("file_out.log", 'a')
    fderr = open("file_err.log", 'a')
    p = subprocess.Popen(cmd, stdout=fdout, stderr=fderr, shell=True)
    if p.poll():
      return
    p.wait()
    return
```

## Python 技巧示例

### Python title()方法

Python title() 方法返回"标题化"的字符串,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle())。

title()方法语法：

```python
str.title()
```

实例:

```python
#!/usr/bin/python

str = "this is string example....wow!!!";
print str.title();
```

> This Is String Example....Wow!!!

## 交换赋值

```python
##不推荐
temp = a
a = b
b = a

##推荐
a, b = b, a  #  先生成一个元组(tuple)对象，然后unpack
```

## Unpacking

```python
##不推荐
l = ['David', 'Pythonista', '+1-514-555-1234']
first_name = l[0]
last_name = l[1]
phone_number = l[2]

##推荐
l = ['David', 'Pythonista', '+1-514-555-1234']
first_name, last_name, phone_number = l
# Python 3 Only
first, *middle, last = another_list
```

## 使用操作符 in

```python
##不推荐
if fruit == "apple" or fruit == "orange" or fruit == "berry":
    # 多次判断

##推荐
if fruit in ["apple", "orange", "berry"]:
    # 使用 in 更加简洁
```

## 字符串操作

```python
##不推荐
colors = ['red', 'blue', 'green', 'yellow']

result = ''
for s in colors:
    result += s  #  每次赋值都丢弃以前的字符串对象, 生成一个新对象

##推荐
colors = ['red', 'blue', 'green', 'yellow']
result = ''.join(colors)  #  没有额外的内存分配
```

## 字典键值列表

```python
##不推荐
for key in my_dict.keys():
    #  my_dict[key] ...

##推荐
for key in my_dict:
    #  my_dict[key] ...

# 只有当循环中需要更改key值的情况下，我们需要使用 my_dict.keys()
# 生成静态的键值列表。
```

## 字典键值判断

```python
##不推荐
if my_dict.has_key(key):
    # ...do something with d[key]

##推荐
if key in my_dict:
    # ...do something with d[key]
```

## 字典 get 和 setdefault 方法

```python
##不推荐
navs = {}
for (portfolio, equity, position) in data:
    if portfolio not in navs:
            navs[portfolio] = 0
    navs[portfolio] += position * prices[equity]
##推荐
navs = {}
for (portfolio, equity, position) in data:
    # 使用 get 方法
    navs[portfolio] = navs.get(portfolio, 0) + position * prices[equity]
    # 或者使用 setdefault 方法
    navs.setdefault(portfolio, 0)
    navs[portfolio] += position * prices[equity]
```

## 判断真伪

```python
##不推荐
if x == True:
    # ....
if len(items) != 0:
    # ...
if items != []:
    # ...

##推荐
if x:
    # ....
if items:
    # ...
```

## 遍历列表以及索引

```python
##不推荐
items = 'zero one two three'.split()
# method 1
i = 0
for item in items:
    print i, item
    i += 1
# method 2
for i in range(len(items)):
    print i, items[i]

##推荐
items = 'zero one two three'.split()
for i, item in enumerate(items):
    print i, item
```

### 列表推导

```python
##不推荐
new_list = []
for item in a_list:
    if condition(item):
        new_list.append(fn(item))

##推荐
new_list = [fn(item) for item in a_list if condition(item)]
```

## 列表推导-嵌套

```python
##不推荐
for sub_list in nested_list:
    if list_condition(sub_list):
        for item in sub_list:
            if item_condition(item):
                # do something...
##推荐
gen = (item for sl in nested_list if list_condition(sl) \
            for item in sl if item_condition(item))
for item in gen:
    # do something...
```

## 循环嵌套

```python
##不推荐
for x in x_list:
    for y in y_list:
        for z in z_list:
            # do something for x &amp;amp; y

##推荐
from itertools import product
for x, y, z in product(x_list, y_list, z_list):
    # do something for x, y, z
```

## 尽量使用生成器代替列表

```python
##不推荐
def my_range(n):
    i = 0
    result = []
    while i &amp;lt; n:
        result.append(fn(i))
        i += 1
    return result  #  返回列表

##推荐
def my_range(n):
    i = 0
    result = []
    while i &amp;lt; n:
        yield fn(i)  #  使用生成器代替列表
        i += 1
*尽量用生成器代替列表，除非必须用到列表特有的函数。
```

## 使用 any/all 函数

```python
##不推荐
found = False
for item in a_list:
    if condition(item):
        found = True
        break
if found:
    # do something if found...

##推荐
if any(condition(item) for item in a_list):
    # do something if found...
```

## 使用 with 处理文件打开

```python
##不推荐
f = open("some_file.txt")
try:
    data = f.read()
    # 其他文件操作..
finally:
    f.close()

##推荐
with open("some_file.txt") as f:
    data = f.read()
    # 其他文件操作...
```

## 计算 x 的 n 次方

```python
def power(x,n)
  s = 1
  while n > 0:
    n = n -1
    s = s * x
    return s
```

## 计算 a*a + b*b + c\*c +

```python
def clac(*numbers):
  sum = 0
  for n in numbers:
    sum = sum + n * n
    return sum
```

## 列出当前目录下所有文件和目录名

```python
[d for d in os.listdir('.')]
```

## 将 list 中所有的字符转换成小写

```python
L = ['Hello', 'World', 'IBM', 'Apple']
[s.lower() for s in L]
```

## 列出某个路径下的所有文件和文件夹的路径

```python
def print_dir():
  filepath = input("Please key in a path: ")
  if filepath =="":
    print("please key in current path!")
  else:
    for i in os.listdir(filepath):
      print(os.path.join(filepath,i))

print(print_dir())
```

## 替换列表中的 3 为 3a

```python
num = [1,2,3,4,5,6,7,8,9,2,1,4,5,3]

# print(num.count(3))
# print(num.index(3))

for i in range(num.count(3)):
  ele_index = num.index(3)
  num[ele_index]="3a"
  print(num)
```

## 打印列表中的所有项目

```python
L = ["aa", "bb", "cc"]
for i in range(len(L)):
  print('List Has %s'%L[i])
```

## 列表合并去重

```python
list1 = [11, 22 ,33, 44, 55]
list2 = [44, 55, 66, 77, 88]

list3 = list1 + list2
print list3
#print(set(list3))
print(list(set(list3)))
```

## 进制转换

```python
dec = int(input("Please key in a number: "))

print("十进制数为: ", dec)
print("转换为二进制为: ", bin(dec))
print("转换为八进制为: ", oct(dec))
print("转换为十六进制为: ", hex(dec))
```

## Python 字符串拼接 % 方式

```python
print('%s %s' % ('Hello', 'world'))
>>> Hello world
```

%号格式化字符串的方式继承自古老的 C 语言，这在很多编程语言都有类似的实现。上例的%s 是一个占位符，它仅代表一段字符串，并不是拼接的实际内容。实际的拼接内容在一个单独的%号后面，放在一个元组里。

类似的占位符还有：%d（代表一个整数）、%f（代表一个浮点数）、%x（代表一个 16 进制数），等等。
%占位符既是这种拼接方式的特点，同时也是其限制，因为每种占位符都有特定意义，实际使用起来太麻烦了。

## Python 字符串 format()拼接方式

```python
# 简洁版
s1 = 'Hello {}! My name is {}.'.format('World', 'Python猫')
print(s1)
>>>Hello World! My name is Python猫.

# 对号入座版
s2 = 'Hello {0}! My name is {1}.'.format('World', 'Python猫')
s3 = 'Hello {name1}! My name is {name2}.'.format(name1='World', name2='Python猫')
print(s2)
>>>Hello World! My name is Python猫.
print(s3)
>>>Hello World! My name is Python猫.
```

这种方式使用花括号{}做占位符，在 format 方法中再转入实际的拼接值。容易看出，它实际上是对%号拼接方式的改进。这种方式在 Python2.6 中开始引入。

上例中，简洁版的花括号中无内容，缺点是容易弄错次序。对号入座版主要有两种，一种传入序列号，一种则使用 key-value 的方式。实战中，我们更推荐后一种，既不会数错次序，又更直观可读。

## Python 字符串拼接 () 类似元组方式

```python
s_tuple = ('Hello', ' ', 'world')
s_like_tuple = ('Hello' ' ' 'world')

print(s_tuple)
>>>('Hello', ' ', 'world')
print(s_like_tuple)
>>>Hello world

type(s_like_tuple) >>>str
```

注意，上例中 s_like_tuple 并不是一个元组，因为元素间没有逗号分隔符，这些元素间可以用空格间隔，也可以不要空格。使用 type()查看，发现它就是一个 str 类型。我没查到这是啥原因，猜测或许()括号中的内容是被 Python 优化处理了。

这种方式看起来很快捷，但是，括号()内要求元素是真实字符串，不能混用变量，所以不够灵活。

```python
# 多元素时，不支持有变量
str_1 = 'Hello'
str_2 = (str_1 'world')
>>> SyntaxError: invalid syntax
str_3 = (str_1 str_1)
>>> SyntaxError: invalid syntax
# 但是下面写法不会报错
str_4 = (str_1)
```

## Python 字符串拼接面向对象模板拼接

```python
from string import Template
s = Template('${s1} ${s2}!')
print(s.safe_substitute(s1='Hello',s2='world'))
>>> Hello world!
```

## Python 字符串拼接常用的+号方式

```python
str_1 = 'Hello world！ '
str_2 = 'My name is Python猫.'
print(str_1 + str_2)
>>>Hello world！ My name is Python猫.
print(str_1)
>>>Hello world！
```

## Python 字符串拼接 join()拼接方式

```python
str_list = ['Hello', 'world']
str_join1 = ' '.join(str_list)
str_join2 = '-'.join(str_list)
print(str_join1) >>>Hello world
print(str_join2) >>>Hello-world
```

str 对象自带的 join()方法，接受一个序列参数，可以实现拼接。拼接时，元素若不是字符串，需要先转换一下。可以看出，这种方法比较适用于连接序列对象中（例如列表）的元素，并设置统一的间隔符。

当拼接长度超过 20 时，这种方式基本上是首选。不过，它的缺点就是，不适合进行零散片段的、不处于序列集合的元素拼接。

## Python 字符串拼接 f-string 方式

```python
name = 'world'
myname = 'python_cat'
words = f'Hello {name}. My name is {myname}.'
print(words)
>>> Hello world. My name is python_cat.
```

f-string 方式出自 PEP 498（Literal String Interpolation，字面字符串插值），从 Python3.6 版本引入。其特点是在字符串前加 f 标识，字符串中间则用花括号{}包裹其它字符串变量。

这种方式在可读性上秒杀 format()方式，处理长字符串的拼接时，速度与 join()方法相当。

尽管如此，这种方式与其它某些编程语言相比，还是欠优雅，因为它引入了一个 f 标识。而其它某些程序语言可以更简练，比如 shell：

```bash
name="world"
myname="python_cat"
words="Hello ${name}. My name is ${myname}."
echo $words
>>>Hello world. My name is python_cat.
```

总结一下，我们前面说的“字符串拼接”，其实是从结果上理解。若从实现原理上划分的话，我们可以将这些方法划分出三种类型：

格式化类：`%、format()、template`
拼接类：`+、()、join()`
插值类：`f-string`
当要处理字符串列表等序列结构时，采用 join()方式；拼接长度不超过 20 时，选用+号操作符方式；长度超过 20 的情况，高版本选用 f-string，低版本时看情况使用 format()或 join()方式。

## python 输出 shell 命令执行结果

```python
import os,subprocess
p = subprocess.Popen("df -h", shell=True, stdout=subprocess.PIPE)
out = p.stdout.readlines()

for line in out:
    print line.strip()
　　将df -h的执行结果输出到stdout,再用readlines方法读出来，再print出来


import os,subprocess
x = subprocess.check_output(["date", "+%T"])
print x
　　这个方法可以将某个命令的结果输出到python
```
