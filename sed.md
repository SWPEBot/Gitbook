# Linux Command 

## Sed Usage

### 1. sed介绍

  sed可删除(delete)、改变(change)、添加(append)、插入(insert)、合、交换文件中的资料行,或读入其它档的资料到文件中,也可替换(substuite)它们其中的字串、或转换(tranfer)其中的字母等等。

### 2. 命令概述

命令行概述:
    sed 编辑指令的格式如下 :
              [address1[,address2]]function[argument]
其中 , 位址参数 address1 、address2 为行数或 regular expression 字串 , 表示所执行编辑的资料行; 函数参数 function[argument] 为 sed 的内定函数 , 表示执行的编辑动作。

注意:

- **-i** : 直接在文件上编辑 （edit files in place）
- **-e**[默认选项]：只在命令行输出，而文件不改变（add the script to the commands to be executed）

#### 2.1 删除

```bash
1.sed -i '1d' file   #删除第一行
2.sed -i 'xd' file   #删除第X行
3.sed -i '1,3d' file  #删除第一到第三行
4.sed -i '/#/d' file  #删除含有'#'号的行
5.sed -i '/xx/!d' file  #删除含有字符串'xx'的所有行
6.sed -i '/word1/,/word2/d' file  #删除从含有单词'word1'到含有单词'word2'的行
7.sed -i '10,/word1/d' file  #删除文件中第10行到含有'word1'的行
8.sed -i '/word1/,10/d' file  #删除文件中含有'word1'的行到第10行
9.sed -i '/t.*t/d' file  #删除含有两个t的行
```

#### 2.2 替换

##### 2.2.1 行的替换

```bash
1. sed -e '1c/#!/bin/more' file #把第一行替换成#!/bin/more
2. sed -e 'nc/just do it' file #把第n行替换成just do it
3. sed -e '1,10c/I can do it' file  #把1到10行替换成一行:I can do it
4. sed -e '1,10c/I can do it!/nLet'"/'"'s start' file #换成两行(I can do it! Let's start)
5. sed -i '1s/True/Fales/' file #把第1行的True替换成False
```

##### 2.2.2 字符的替换

```bash
1. sed -e 's/word1/& word2/' file 
#将每一行的word1单词替换成s参数最多与两个位置参数相结合,函数参数s中有两个特殊的符号
 # & : 代表pattern
 # /n : 代表 pattern 中被第 n 个 /( 、/)(参照[附录 A]) 所括起来的字串。例如

    sed -e 's/w1/& w2/' file  # w1的地方输出 w1 w2
    sed -e  's//(test/) /(my/) /(car/)/[/2 /3 /1]/' file   #结果: [my car test]
2.参数举例
    sed -e 's/w1/& w2/g' file 
    #g : 代表替换所有匹配项目;这里,文件中所有字符串w1都会被替换成字串w1 w2
    
    sed -e 's/w1/& w2/10' file
    #m(10) : 替换行内第m个符合的字串; 记住，是行内的第m个匹配的字串
    
    sed -e 's/w1/& w2/p' file
    #p : 替换第一个和w1匹配的字符串为w1 w2，并输出到标准输出.
    
    sed -e 's/w1/& w2/w w2file' file
    #w filename : 该参数会将替换过的内容写入到文件w2file并输出替换后的整个文件。注意w2file里写的只是替换过的行。 
    
    sed -e 's/w1/& w2/' file
    #这里的flag 为空, 这样就只是将第一个w1匹配的字符串替换成w1 w2而后面的不进行替换。
3.位置参数应用举例
	sed -e '/machine/s/phi/beta/g' file
    #将文件中含"machine"字串的资料行中的"phi"字串,替换成为"beta"字串
    
    sed -e '1,10 s/w1/& w2/g' file
    #把1到10内的w1字符串替换成w1 w2字符串。
    
    sed -e '1,/else/ s/w1/& w2/g' file
    #把1到字符串else内的w1字符串替换成w1 w2字符串。


```

#### 2.3 内容的插入

```bash
i
    基本格式:
    [address] i/ 插入内容 filename
 word2)
说明:
函数参数 s 表示替换(substitute)文件内字串。其指令格式如下 :
[address1[ ,address2]] s/pattern/replacemen/[flag]

    sed -e '/#/i/words' file      #在#字符的前面插入一行words

说明：
    这里的函数参数是i，它只能有一个地址参数。
    sed -e '1/i/words' file
    在第一行前加一行words
    cat "word" | sed -e '/$/.doc/g'   #输出word.doc
    在word后面加上后缀名，从而输出word.doc
    i 参数正好与a参数相反，它是插入到所给内容的前面.

a
    a参数的使用格式如下：
    [address] a/ <插入内容> filename

    sed -e '/unix/a/ haha' test.txt   #在含有unix的行后添加"haha"
    #输出结果为:
        unix
        haha

    另外: sed -e '1 a/ hh' test.txt  #在第一行后添加hh字符.
          sed -i '1a\\''words' file   #在第一行下插入一行words.
```

#### 2.4 文本的打印: p

  基本格式：
  [address1,[address2]] p

p函数为sed的打印函数，在这里要注意-e 和-n 参数的区别。一般使用-n参数。

```bash
    1 sed -e '/then/ p' filename  #打印所有行并重复打印含有then 的行
    2 sed -n '/then/ p' filename  #只打印含有then的行
    3 sed -e '1,3 p' filename     # 打印所有行并重复1-3行
    4 sed -n '1,3 p' filename     # 打印1-3行
    5 sed -n '/if/,/fi/ p' filename #打印字符if和fi之间的内容
```

#### 2.5 字元的替换: y

```bash
 1 sed -e 'y/abc../xyz../' filename
    把文件中的a字母替换成x, b替换成y, c替换成z。
 2 sed  -e 'y/abc/ABC' filename
    把小写的abc转换成大写的ABC

```

#### 2.6 反相执行命令 : !

  基本格式:
  [address1[ , address2]] ! 函数参数

```bash
 sed -e '/1996/!d' filename #删除除了含有1996的所有行
```

#### 2.7 改变文件中的资料: c

​    基本格式：
​    [address1[ ,address2]]c/ filename
​    函数参数 c 紧接着 "/" 字元用来表示此行结束 , 使用者所输入的资料必须从下一行输入。如果资料超过一行 , 则须在>每行的结尾加入"/"

```bash
sed -e '/zhengxh/c hhhh' filename
表示把含有字符串zhengxh的行，该成hhhh。
```

#### 2.8 读入下一行资料: n

​    基本格式：
​    [address1[ ,address2]] n

```bash
sed -n -e '/echo/n' -e 'p' temp
表示输出文件，但如果一行含有字符串echo，则输出包含该字符串的下一行。
sed -n -e 'n' -e 'p' filename
输出文中的偶数行
```

#### 3. 命令的复用

​    一次执行多个命令的方式：

```bash
    sed 's/w1/& w2/g; 1/i/words' filename   #(使用;号把命令隔开，注意前面不加-e参数)
    sed -e 'cmd1' -e 'cmd2'  filename     #(使用多个-e参数)
```
