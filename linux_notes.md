# Linux 笔记

## 进制转换

- 将 10 进制转换为十六进制

   `printf %x 12345`

- 将十六进制转换为十进制

   `printf %d 0x06`

## 压缩和解压缩

**事实上，从版本 1.15 开始，tar 可以自动识别压缩格式，因此可以解压缩而无需人为地区分压缩格式.**

e.g:

```bash
tar -xvf filename.tar.gz
tar -xvf filename.tar.bz2
tar -xvf filename.tar.xz
tar -xvf filename.tar.Z
```

### Zip

```bash
sudo apt-get install unzip

# zip

zip -r xxxx.zip

# unzip

unzip xxxx.par/xxx.zip
```

### 归档并压缩到 tar.gz 或 tar.bz2

```bash
# Packaged and compressed in gzip
tar -zcvf test.tar.gz file1 file2

# Packaged and compressed with bzip2
tar -jcvf test.tar.bz2 file1 file2
```

```bash
# Package all files in the test directory, excluding files ending in .log
tar -zcvf test.tar.gz --exclude=test/*.log test/*
```

### 解压 tar.xz

`tar xvJf ***.tar.xz`

### 解压 tar.bz2

`tar Jxvf ***.tar.bz2`

### 解压 gzip

`gzip -d xxxxx.gz`

### About tar

_其中 zxvf 具有以下含义._

|     |                     |                   |
| --- | ------------------: | ----------------- |
| z:  |                gzip | Compressed format |
| x:  |             extract | Unzip             |
| v:  |             verbose | More Information  |
| f:  | file(file=archieve) | File              |
| j:  |               bzip2 | Compressed format |

## 设置文件所有者和权限

1. 更改文件所有者

    `sudo chown -R owner:owner filename`

2. 更改文件权限

    `sudo chmod +x file name`

    [refer link](http://wenson.iteye.com/blog/212739)

## Linux 照片快速查看工具

```sh
# Install

sudo apt-get install feh

# Usage

feh photo-name
```

## 如何使用 curl 获取真实 IP

```bash
curl ipinfo.io
```

## scp 用法示例

`scp -r ./docker_control.tar.bz2 dome@10.17.63.249:/home/dome/swshare` {scp folder}

`scp hello.py root@10.18.1.xx:/home/xx/xx/xx/xxz` {scp file}

## 列出当前目录文件的数量

`ls | wc –l`

## Linux 设置进程后台运行

```shell
<ctrl + z> > bg
```

## 检查 DUT & HOST IP 地址

参考链接: <https://www.cnblogs.com/peida/archive/2013/02/27/2934525.html>

* 常用命令

  ```shell
  ifconfig

  ifconfig | less

  ifconfig -a

  ifconfig | more
  ```

* 管理网卡

  ```shell
  ifconfig eth0 down/up
  ifup eth0
  ifdown eth0
  ```

* 检查路由

  `router`

## 文件权限设定示例

更改文件所有者

`sudo chown -R owner:owner filename`

更改文件权限

`sudo chmod +x file name`

参考链接: <http://wenson.iteye.com/blog/212739>

## Linux touch & mkdir 用法示例

* **touch**

  `touch xxxx #xxx is a new file`

* **mkdir**

  `mkdir` : 创建目录, `rmdir` : 删除目录两个命令都支持 -p 参数，对于 mkdir 命令若指定路径的父目录不存在则一并创建，对于 rmdir 命令则删除指定路径的所有层次目录，如果文件夹里有内容，则不能用 rmdir 命令

  如:`mkdir -p 1/2/3`, `rmdir -p 1/2/3`

## cp 命令用法示例

复制一个文件到另一目录:`cp 1.txt ../test2`

复制一个文件到本目录并改名:`cp 1.txt 2.txt`

复制一个文件夹 a 并改名为 b:`cp -r a b`

**使用 cp 快速 packing toolkit.**

`cp ~/gitinternal/coding/usr/local/factory/ toolkit/usr/local/`

## mv 命令用法示例

将一个文件移动到另一个目录：`mv 1.txt ../test1`

将一个文件在本目录改名：mv `1.txt 2.txt`

将一个文件一定到另一个目录并改名：`mv 1.txt ../test1/2.txt`

## rm 命令用法示例

rm 命令用于删除文件，与 dos 下的`del/erase`命令相似，rm 命令常用的参数有三个：`-i，-r，-f`

`–i` ：系统在删除文件之前会先询问确认，用户回车之后，文件才会真的被删除。需要注意， linux 下删除的文件是不能恢复的，删除之前一定要谨慎确认。

`–r` ：该参数支持目录删除，功能和 rmdir 命令相似。

`–f` ：和 -i 参数相反，-f 表示强制删除

## SSH 用法示例

参考链接: <http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html>

用法示例:

```bash
ssh user@localhost
ssh root@192.168.0.1
```

## While 用法示例

```bash
while true; do cat /sys/cllass/drm/card1-HDMI-A-1/content_protection; sleep 1; done
```

```bash
# bash 多句完成
$while true
>do
>ping www.baidu.com
>done;

# bash 单句完成
while true; do ping www.baidu.com; done;
```

## 命令行常用工具

`ag`：比 grep、ack 更快的递归搜索文件内容

`jq`: json 文件处理以及格式化显示，支持高亮，可以替换 python -m json.tool

`shellcheck`: shell 脚本静态检查工具，能够识别语法错误以及不规范的写法

`yapf`：Google 开发的 python 代码格式规范化工具，支持 pep8 以及 Google 代码风格

`fzf`：命令行下模糊搜索工具，能够交互式智能搜索并选取文件或者内容

`cloc`：代码统计工具，能够统计代码的空行数、注释行、编程语言

`tmux`：终端复用工具，替代 screen、nohup

`script/scriptreplay`: 终端会话录制

`thefuck`：用途是每次命令行打错了以后，打一句 fuck 就会自动更正命令。比如 apt-get 打成了 aptget,fuck 以后自动变成 apt-get, 但还是没加 sudo, 再 fuck，成功！

`black`：python 代码自动优化工具 Black

## 使用 find 命令查找文件

_find 用法:_

切换到终端屏幕<Ctrl + Alt + F2>

以 root 身份登录

`find / -name filename` _search with all directories_

`find -name filename` _search with current directory_

[find usage](http://www.oracle.com/technetwork/cn/topics/calish-find-096463-zhs.html)

## 检查 Linux 内核版本

`uname -r`

## 创建文件软连接（Created Symbol link）

`ln -s file1 file2`

## 使用 tmux 和 screen 后台管理进程

### tmux

```bash
tmux new -s session_name # Open a tmux session
ctrl b + d # keep running in the background and close the current window
tmux list-session # view session session
tmux at -t session_name # back to the session
```

### screen

```bash
#e.g:
screen gitbook serve --port 2019

ctrl a + d # close window keep background running command

screen -ls # View window PID

screen -r PID # reconnect window
```

## Linux 服务器运行状况检查

```bash
# 检查服务器运行时间
uptime

# 检查内核错误
dmesg | tail

# 显示硬件信息
vmstat 1

# 检查 CPU 信息
mpstat -P ALL 1

# 检查使用量
pidstat 1

# 检查 IO 数据
iostat -xz 1

# 检查内存使用状况
free -m

#检查使用状况
top
```

## 调试 ubuntu 没有模块名称 pexpect

```bash
pip install pexpect
sudo apt install python-pip
sudo apt-get install python-distutils-extra
sudo -E pip install pexpect
```

## 监控 Linux server 网络占用

```bash
sudo apt-get install nethogs

sudo nethogs
```
