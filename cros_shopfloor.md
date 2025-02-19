# Chrome OS Shopfloor

## Shopfloor Backend Integration

帮助生产线跟踪 DUT 状态, 并从车间服务器后端检索信息(例如序列号，重要产品数据，母板序列号等等), ODM 工厂必须实现以下 XMLRPC Web 服务

## Shopfloor Service 进程管理

查看端口占用状态

`sudo netstat -anutp | grep port | grep LISTEN`

杀死异常进程

`sudo kill -9 port`

使用系统权限后台运行 shopfloor service

`sudo ./shopfloor_service.py &`

监视 shopfloor service 进程

```bash
# 方法一
nohup command &
# log 记录会保存在nohup.out 文件中

# 方法二
sudo ./shopfloor_serveice.py >> logfile 2>&1
```

## 调试 linux umount show device is busy

```bash
# method 1
sudo fuser -m -k /mnt/factorybundle/ #will auto kill busy process

# method 2
fuser -m -v -i -k /media/BAK/  #will tell you select y/n

# method 3
fuser ／mnt/dir #recommend
```

## Shopfloor umount is busy, make sync fail debug

_Only for python framework_

```bash
sudo vimdiff ./factory_setup/make_factory_package.sh ../cyan/factory_setup/make_factory_package.sh
```

`grep 'sleep10'` {Modify sleep 10}

## Shopfloor 进程异常中断问题处理

> 修改 Linux server 文件同时打开数量, [Error 24: too many open files]

[参考链接](https://blog.csdn.net/qq_23926575/article/details/76619827)

在终端输入 ulimit -n 来查看当前系统默认的最大文件数量，一般默认为 1024.

### 方式一

```bash
ulimit -n 8192
# 只适用服务器，且重启需重新设定
```

### 方式二

> `sudo vim /etc/security/limits.conf` 在末尾插入

```bash
* soft nofile 8192
* hard nofile 8192
# 保存重启后永久生效
```

`grep 'sleep10'` {Modify sleep 10}

## Python 架构手动获取 shopfloor url

```python
import shopfloor
shopfloor.get_server_url()
```

## 手动检查 Finalize 以及工厂流程

* Python 架构用法示例

  `gooftool finalize`

* Json 架构用法示例

  `gooftool wipe_in_place --shopfloor_url xxxxxx`

## 测试 Finalize 及 Finalize FQC

```bash
/usr/local/factory/sh/cutoff/inform_shopfloor.sh
```

## 检查 shopfloor 进程 PID

```bash
sudo netstat -anutp | grep xxxx

sudo ls -alt /proc/xxxx
```

## 调整服务器网络缓存

`echo 4096 > /proc/sys/net/ipv4/neigh/default/gc_thresh3`
