# Bash Shell Notes

## Shell Script Example

注意事项

1）开头加解释器：#!/bin/bash

2）语法缩进，使用四个空格，Google 使用两个空格；多加注释说明。

3）命名建议规则：变量名大写、局部变量小写，函数名小写，名字体现出实际作用。

4）默认变量是全局的，在函数中变量 local 指定为局部变量，避免污染其他作用域。

5）有两个命令能帮助我调试脚本：set -e 遇到执行非 0 时退出脚本，set-x 打印执行过程。

6）写脚本一定先测试再到生产上。

### Shell 执行命令技巧

```bash
后一个命令依赖于前一个命令的输出，可以是用管道 (|)
ls | wc -l  #当前目录文件个数

后一个命令必须等前一个命令运行成功后在运行，可以使用双与号 (&&)
aa && ls    #只运行aa，ls不运行

后一个命令必须等前一个命令运行完，不关心是否成功，使用单与号 (&)
aa & ls     #aa和ls都运行，但是ls必须等aa运行完。

并行执行多个命令，使用两个竖号 (||)
aa || ls    #aa和ls并行执行，互不影响。
```

### 获取随机字符串或数字

获取随机 8 位字符串：

```bash
方法1：
# echo $RANDOM |md5sum |cut -c 1-8
471b94f2
方法2：
# openssl rand -base64 4
vg3BEg==
方法3：
# cat /proc/sys/kernel/random/uuid |cut -c 1-8
ed9e032c
```

获取随机 8 位数字：

```bash
方法1：
# echo $RANDOM |cksum |cut -c 1-8
23648321
方法2：
# openssl rand -base64 4 |cksum |cut -c 1-8
38571131
方法3：
# date +%N |cut -c 1-8
69024815
```

cksum：打印 CRC 效验和统计字节

### 定义一个颜色输出字符串函数

```bash
方法1：
function echo_color() {
    if [ $1 == "green" ]; then
        echo -e "\033[32;40m$2\033[0m"
    elif [ $1 == "red" ]; then
        echo -e "\033[31;40m$2\033[0m"
    fi
}
方法2：
function echo_color() {
    case $1 in
        green)
            echo -e "\033[32;40m$2\033[0m"
            ;;
        red)
            echo -e "\033[31;40m$2\033[0m"
            ;;
        *)
            echo "Example: echo_color red string"
    esac
}
使用方法：echo_color green "test"
```

function 关键字定义一个函数，可加或不加。

### 检查软件包是否安装

```bash
#!/bin/bash
if rpm -q sysstat &>/dev/null; then
    echo "sysstat is already installed."
else
    echo "sysstat is not installed!"
fi
```

### 检查服务状态

```bash
#!/bin/bash
PORT_C=$(ss -anu |grep -c 123)
PS_C=$(ps -ef |grep ntpd |grep -vc grep)
if [ $PORT_C -eq 0 -o $PS_C -eq 0 ]; then
    echo "内容" | mail -s "主题" dst@example.com
fi
```

### 检查主机存活状态

方法 1：将错误 IP 放到数组里面判断是否 ping 失败三次

```bash
#!/bin/bash
IP_LIST="192.168.18.1 192.168.1.1 192.168.18.2"
for IP in $IP_LIST; do
    NUM=1
    while [ $NUM -le 3 ]; do
        if ping -c 1 $IP > /dev/null; then
            echo "$IP Ping is successful."
            break
        else
            # echo "$IP Ping is failure $NUM"
            FAIL_COUNT[$NUM]=$IP
            let NUM++
        fi
    done
    if [ ${#FAIL_COUNT[*]} -eq 3 ];then
        echo "${FAIL_COUNT[1]} Ping is failure!"
        unset FAIL_COUNT[*]
    fi
done
```

方法 2：将错误次数放到 FAIL_COUNT 变量里面判断是否 ping 失败三次

```bash
#!/bin/bash
IP_LIST="192.168.18.1 192.168.1.1 192.168.18.2"
for IP in $IP_LIST; do
    FAIL_COUNT=0
    for ((i=1;i<=3;i++)); do
        if ping -c 1 $IP >/dev/null; then
            echo "$IP Ping is successful."
            break
        else
            # echo "$IP Ping is failure $i"
            let FAIL_COUNT++
        fi
    done
    if [ $FAIL_COUNT -eq 3 ]; then
        echo "$IP Ping is failure!"
    fi
done
```

方法 3：利用 for 循环将 ping 通就跳出循环继续，如果不跳出就会走到打印 ping 失败

```bash
#!/bin/bash
ping_success_status() {
    if ping -c 1 $IP >/dev/null; then
        echo "$IP Ping is successful."
        continue
    fi
}
IP_LIST="192.168.18.1 192.168.1.1 192.168.18.2"
for IP in $IP_LIST; do
    ping_success_status
    ping_success_status
    ping_success_status
    echo "$IP Ping is failure!"
done
```

### 监控 CPU、内存和硬盘利用率

1）CPU, 借助 vmstat 工具来分析 CPU 统计信息。

```bash
#!/bin/bash
DATE=$(date +%F" "%H:%M)
IP=$(ifconfig eth0 |awk -F '[ :]+' '/inet addr/{print $4}')  # 只支持CentOS6
MAIL="example@mail.com"
if ! which vmstat &>/dev/null; then
    echo "vmstat command no found, Please install procps package."
    exit 1
fi
US=$(vmstat |awk 'NR==3{print $13}')
SY=$(vmstat |awk 'NR==3{print $14}')
IDLE=$(vmstat |awk 'NR==3{print $15}')
WAIT=$(vmstat |awk 'NR==3{print $16}')
USE=$(($US+$SY))
if [ $USE -ge 50 ]; then
    echo "
    Date: $DATE
    Host: $IP
    Problem: CPU utilization $USE
    " | mail -s "CPU Monitor" $MAIL
fi
```

2）内存

```bash
#!/bin/bash
DATE=$(date +%F" "%H:%M)
IP=$(ifconfig eth0 |awk -F '[ :]+' '/inet addr/{print $4}')
MAIL="example@mail.com"
TOTAL=$(free -m |awk '/Mem/{print $2}')
USE=$(free -m |awk '/Mem/{print $3-$6-$7}')
FREE=$(($TOTAL-$USE))
# 内存小于1G发送报警邮件
if [ $FREE -lt 1024 ]; then
    echo "
    Date: $DATE
    Host: $IP
    Problem: Total=$TOTAL,Use=$USE,Free=$FREE
    " | mail -s "Memory Monitor" $MAIL
fi
```

3）硬盘

```bash
#!/bin/bash
DATE=$(date +%F" "%H:%M)
IP=$(ifconfig eth0 |awk -F '[ :]+' '/inet addr/{print $4}')
MAIL="example@mail.com"
TOTAL=$(fdisk -l |awk -F'[: ]+' 'BEGIN{OFS="="}/^Disk \/dev/{printf "%s=%sG,",$2,$3}')
PART_USE=$(df -h |awk 'BEGIN{OFS="="}/^\/dev/{print $1,int($5),$6}')
for i in $PART_USE; do
    PART=$(echo $i |cut -d"=" -f1)
    USE=$(echo $i |cut -d"=" -f2)
    MOUNT=$(echo $i |cut -d"=" -f3)
    if [ $USE -gt 80 ]; then
        echo "
        Date: $DATE
        Host: $IP
        Total: $TOTAL
        Problem: $PART=$USE($MOUNT)
        " | mail -s "Disk Monitor" $MAIL
    fi
done
```

### 批量主机磁盘利用率监控

前提监控端和被监控端 SSH 免交互登录或者密钥登录。

写一个配置文件保存被监控主机 SSH 连接信息，文件内容格式：IP User Port

```bash
#!/bin/bash
HOST_INFO=host.info
for IP in $(awk '/^[^#]/{print $1}' $HOST_INFO); do
    USER=$(awk -v ip=$IP 'ip==$1{print $2}' $HOST_INFO)
    PORT=$(awk -v ip=$IP 'ip==$1{print $3}' $HOST_INFO)
    TMP_FILE=/tmp/disk.tmp
    ssh -p $PORT $USER@$IP 'df -h' > $TMP_FILE
    USE_RATE_LIST=$(awk 'BEGIN{OFS="="}/^\/dev/{print $1,int($5)}' $TMP_FILE)
    for USE_RATE in $USE_RATE_LIST; do
        PART_NAME=${USE_RATE%=*}
        USE_RATE=${USE_RATE#*=}
        if [ $USE_RATE -ge 80 ]; then
            echo "Warning: $PART_NAME Partition usage $USE_RATE%!"
        fi
    done
done
```

## 使用 bash shell 重复运行 5 次命令

```bash
# 方法 1:
seq 5 | xargs -i ls

# 方法 2:
for n in {1..5}; do echo “Hello World!”; sleep 1; done
```

## Shell 脚本简单示例

注意事项

1）开头加解释器：#!/bin/bash

2）语法缩进，使用四个空格，Google 使用两个空格；多加注释说明。

3）命名建议规则：变量名大写、局部变量小写，函数名小写，名字体现出实际作用。

4）默认变量是全局的，在函数中变量 local 指定为局部变量，避免污染其他作用域。

5）有两个命令能帮助我调试脚本：set -e 遇到执行非 0 时退出脚本，set-x 打印执行过程。

6）写脚本一定先测试再到生产上。

### 批量创建用户

```bash
#!/bin/bash
DATE=$(date +%F_%T)
USER_FILE=user.txt
echo_color(){
    if [ $1 == "green" ]; then
        echo -e "\033[32;40m$2\033[0m"
    elif [ $1 == "red" ]; then
        echo -e "\033[31;40m$2\033[0m"
    fi
}
# 如果用户文件存在并且大小大于0就备份
if [ -s $USER_FILE ]; then
    mv $USER_FILE ${USER_FILE}-${DATE}.bak
    echo_color green "$USER_FILE exist, rename ${USER_FILE}-${DATE}.bak"
fi
echo -e "User\tPassword" >> $USER_FILE
echo "----------------" >> $USER_FILE
for USER in user{1..10}; do
    if ! id $USER &>/dev/null; then
        PASS=$(echo $RANDOM |md5sum |cut -c 1-8)
        useradd $USER
        echo $PASS |passwd --stdin $USER &>/dev/null
        echo -e "$USER\t$PASS" >> $USER_FILE
        echo "$USER User create successful."
    else
        echo_color red "$USER User already exists!"
    fi
done
```

## Use bash shell run 5 times command

```bash
# Method 1:
seq 5 | xargs -i ls

# Method 2:
for n in {1..5}; do echo “Hello World!”; sleep 1; done
```

## Set the bash history print time

```bash
export HISTTIMEFORMAT='%F %T'
history | more
```

## 设置历史记录打印时间

```bash
export HISTTIMEFORMAT='%F %T'
history | more
```
