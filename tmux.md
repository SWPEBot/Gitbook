# Tmux 用法示例

一、 命令介绍：

Tmux ("Terminal Multiplexer"的简称), 是一款优秀的终端复用软件，类似 GNU screen，但比 screen 更出色。
tmux 来自于 OpenBSD，采用 BSD 授权。使用它最直观的好处就是, 通过一个终端登录远程主机并运行 tmux 后，在其中可以开启多个控制台而无需再“浪费”多余的终端来连接这台远程主机,
还有一个好处就是当终端关闭后该 shell 里面运行的任务进程也会随之中断，通过使用 tmux 就能很容易的解决这个问题。

二、 使用场景：

1. 关闭终端,再次打开时原终端里面的任务进程依然不会中断 ;

2. 处于异地的两人可以对同一会话进行操作，一方的操作另一方可以实时看到 ;

3. 可以在单个屏幕的灵活布局下开出很多终端，然后就能协作地使用它们 ;

三、 命令用法：

安装:`sudo apt-get install tmux`

查看命令的用法:

```bash
tmux --help
usage: tmux [-28lquvV] [-c shell-command] [-f file] [-L socket-name]
            [-S socket-path] [command [flags]]
```

个别选项及参数介绍：

1.运行 tmux: `tmux`

2.新建会话：
`tmux new -s SESSION-NAME`
`tmux new -s second-tmux`

3.查看已创建的会话：`tmux ls`

4.进入一个已知会话：`tmux a -t SESSION-NAME` 或 `tmux attach -t SESSION-NAME`

```bash
tmux ls
0: 1 windows (created Wed Aug 30 11:15:29 2017) [61x16]
second-tmux: 1 windows (created Wed Aug 30 11:23:51 2017) [85x16]
tmux a -t second-tmux
```

5.暂时离开当前会话：

(该命令会从当前会话中退出去, 因此才会有稍后重新接入会话这么一说 )

`tmux detach`

6.关闭会话：

`tmux kill-session -t SESSION-NAME`

( 在会话内部或外部执行均可)

```bash
tmux ls
0: 1 windows (created Wed Aug 30 11:15:29 2017) [61x16]
second-tmux: 1 windows (created Wed Aug 30 11:40:24 2017) [85x16]
tmux kill-session -t second-tmux
tmux ls
0: 1 windows (created Wed Aug 30 11:15:29 2017) [61x16]
```

注：1.单独运行 tmux 命令，即开启一个 tmux 会话 ; 2. 不能在 tmux 会话里面再新建会话，会报错："sessions should be nested with care, unset \$TMUX to force"

四、 分屏操作：

很多情况下, 需要在一个会话中运行多个命令，执行多个任务,我们可以在一个会话的多个窗口里组织他们。

分屏：分为水平分屏和垂直分屏

水平分屏

> 快捷键：先按 ctrl+b, 放开后再按%

垂直分屏

> 快捷键：先按 ctrl+b, 放开后再按 "

分屏后的窗口中的光标互相切换

> 快捷键：先按 ctrl+b, 放开后再按下 o

切换 tmux 会话终端

> 快捷键：先按 ctrl+b, 放开后再按 s

终端内显示时间

> 快捷键：先按 ctrl+b, 放开后再按 t
> 退出时间界面：按 q 键

五. 其他快捷键操作

终止一个终端窗口(需确认)

> 快捷键：exit 或 先按 ctrl+b, 放开后再按 &

在当前窗口的基础上再打开一个新的窗口

> 快捷键：先按 ctrl+b, 放开后再按 c

暂时退出当前会话

> 快捷键：先按 ctrl+b, 放开后再按 d

查看面板编号

> 快捷键：先按 ctrl+b, 放开后再按 q

关闭所有分屏后的窗口，即合并为一个窗口

> 快捷键：先按 ctrl+b, 放开后再按！
