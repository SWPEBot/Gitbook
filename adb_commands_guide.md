# ADB 常用指令完整速查手册

> ADB = Android Debug Bridge，用于通过电脑调试、控制 Android 设备。

---

## 目录

- [1. 设备连接与状态](#1-设备连接与状态)
- [2. 多设备指定](#2-多设备指定)
- [3. 无线调试 / TCP 连接](#3-无线调试--tcp-连接)
- [4. Shell 相关](#4-shell-相关)
- [5. 文件传输](#5-文件传输)
- [6. APK 安装与卸载](#6-apk-安装与卸载)
- [7. 应用包管理 pm](#7-应用包管理-pm)
- [8. 启动 Activity / Service / Broadcast](#8-启动-activity--service--broadcast)
- [9. 输入模拟 input](#9-输入模拟-input)
- [10. 截图与录屏](#10-截图与录屏)
- [11. 日志 logcat](#11-日志-logcat)
- [12. dumpsys 系统信息](#12-dumpsys-系统信息)
- [13. 设备信息查询](#13-设备信息查询)
- [14. 网络相关](#14-网络相关)
- [15. 端口转发](#15-端口转发)
- [16. Bugreport / 诊断](#16-bugreport--诊断)
- [17. 进程与性能](#17-进程与性能)
- [18. settings 系统设置](#18-settings-系统设置)
- [19. 常亮 / 不息屏设置](#19-常亮--不息屏设置)
- [20. 电源与重启](#20-电源与重启)
- [21. Fastboot 相关](#21-fastboot-相关)
- [22. SQLite 数据库](#22-sqlite-数据库)
- [23. run-as 调试应用数据](#23-run-as-调试应用数据)
- [24. UI 自动化辅助](#24-ui-自动化辅助)
- [25. 剪贴板](#25-剪贴板)
- [26. 模拟 GPS / 定位](#26-模拟-gps--定位)
- [27. 模拟器 adb emu](#27-模拟器-adb-emu)
- [28. 权限与 AppOps](#28-权限与-appops)
- [29. 存储相关](#29-存储相关)
- [30. 账号、用户、多用户](#30-账号用户多用户)
- [31. 常用组合命令](#31-常用组合命令)
- [32. 常见问题处理](#32-常见问题处理)
- [33. 最常用 20 条](#33-最常用-20-条)

---

## 1. 设备连接与状态

### 查看已连接设备

```bash
adb devices
```

### 查看详细设备信息

```bash
adb devices -l
```

### 查看设备状态

```bash
adb get-state
```

可能返回：

```text
device
offline
unknown
```

### 等待设备连接

```bash
adb wait-for-device
```

### 重启 ADB 服务

```bash
adb kill-server
adb start-server
```

### 查看 adb 版本

```bash
adb version
```

### 重新连接设备

```bash
adb reconnect
```

### 重连指定状态设备

```bash
adb reconnect device
adb reconnect offline
```

---

## 2. 多设备指定

当电脑连接多个 Android 设备或模拟器时，需要指定设备序列号。

### 查看设备序列号

```bash
adb devices
```

### 指定某台设备执行命令

```bash
adb -s <device_serial> shell
adb -s <device_serial> install app.apk
```

### 指定 USB 设备

```bash
adb -d <command>
```

### 指定模拟器

```bash
adb -e <command>
```

### 指定具体设备

```bash
adb -s <serial> <command>
```

---

## 3. 无线调试 / TCP 连接

### USB 切换到 TCP 模式

```bash
adb tcpip 5555
```

### 连接无线设备

```bash
adb connect <ip>:5555
```

示例：

```bash
adb connect 192.168.1.100:5555
```

### 断开无线连接

```bash
adb disconnect
adb disconnect <ip>:5555
```

### 查看设备 IP

```bash
adb shell ip addr show wlan0
```

或：

```bash
adb shell ifconfig wlan0
```

### 恢复 USB 模式

```bash
adb usb
```

---

## 4. Shell 相关

### 进入设备 shell

```bash
adb shell
```

### 直接执行 shell 命令

```bash
adb shell <command>
```

示例：

```bash
adb shell ls /sdcard
adb shell pwd
adb shell whoami
adb shell uname -a
```

### 切换 root，需设备支持

```bash
adb root
adb unroot
```

### 重新挂载系统分区，需 root

```bash
adb remount
```

---

## 5. 文件传输

### 电脑传文件到手机

```bash
adb push <local_path> <remote_path>
```

示例：

```bash
adb push test.txt /sdcard/
```

### 手机拉文件到电脑

```bash
adb pull <remote_path> <local_path>
```

示例：

```bash
adb pull /sdcard/test.txt .
```

### 删除设备文件

```bash
adb shell rm /sdcard/test.txt
```

### 创建设备目录

```bash
adb shell mkdir /sdcard/test
```

### 查看文件

```bash
adb shell ls -la /sdcard/
adb shell cat /sdcard/test.txt
```

---

## 6. APK 安装与卸载

### 安装 APK

```bash
adb install app.apk
```

### 覆盖安装

```bash
adb install -r app.apk
```

### 允许降级安装

```bash
adb install -d app.apk
```

### 允许安装测试包

```bash
adb install -t app.apk
```

### 安装到 SD 卡

```bash
adb install -s app.apk
```

### 多 APK / Split APK 安装

```bash
adb install-multiple base.apk split_config.apk
```

### 卸载应用

```bash
adb uninstall <package_name>
```

### 卸载但保留数据

```bash
adb uninstall -k <package_name>
```

---

## 7. 应用包管理 pm

### 查看所有包名

```bash
adb shell pm list packages
```

### 查看第三方应用

```bash
adb shell pm list packages -3
```

### 查看系统应用

```bash
adb shell pm list packages -s
```

### 按关键词过滤

Linux / macOS：

```bash
adb shell pm list packages | grep keyword
```

Windows CMD：

```cmd
adb shell pm list packages | findstr keyword
```

### 查看 APK 路径

```bash
adb shell pm path <package_name>
```

### 查看应用详细信息

```bash
adb shell dumpsys package <package_name>
```

### 清除应用数据

```bash
adb shell pm clear <package_name>
```

### 禁用应用

```bash
adb shell pm disable-user <package_name>
```

### 启用应用

```bash
adb shell pm enable <package_name>
```

### 隐藏应用

```bash
adb shell pm hide <package_name>
```

### 取消隐藏应用

```bash
adb shell pm unhide <package_name>
```

### 授权权限

```bash
adb shell pm grant <package_name> <permission>
```

示例：

```bash
adb shell pm grant com.example.app android.permission.CAMERA
```

### 撤销权限

```bash
adb shell pm revoke <package_name> <permission>
```

---

## 8. 启动 Activity / Service / Broadcast

核心工具：`am`。

### 启动 App

```bash
adb shell monkey -p <package_name> -c android.intent.category.LAUNCHER 1
```

### 启动指定 Activity

```bash
adb shell am start -n <package_name>/<activity_name>
```

示例：

```bash
adb shell am start -n com.example.app/.MainActivity
```

### 启动 URL

```bash
adb shell am start -a android.intent.action.VIEW -d "https://www.baidu.com"
```

### 启动拨号

```bash
adb shell am start -a android.intent.action.DIAL -d tel:10086
```

### 启动 Service

```bash
adb shell am startservice -n <package_name>/<service_name>
```

### Android 8+ 启动前台服务

```bash
adb shell am start-foreground-service -n <package_name>/<service_name>
```

### 停止 Service

```bash
adb shell am stopservice -n <package_name>/<service_name>
```

### 发送广播

```bash
adb shell am broadcast -a <action>
```

示例：

```bash
adb shell am broadcast -a android.intent.action.BOOT_COMPLETED
```

### 强制停止应用

```bash
adb shell am force-stop <package_name>
```

### 杀掉后台进程

```bash
adb shell am kill <package_name>
```

---

## 9. 输入模拟 input

### 点击屏幕

```bash
adb shell input tap <x> <y>
```

示例：

```bash
adb shell input tap 500 1200
```

### 滑动屏幕

```bash
adb shell input swipe <x1> <y1> <x2> <y2> <duration_ms>
```

示例：

```bash
adb shell input swipe 500 1500 500 500 300
```

### 输入文本

```bash
adb shell input text "hello"
```

空格需要写成 `%s`：

```bash
adb shell input text "hello%sworld"
```

### 按键事件

```bash
adb shell input keyevent <keycode>
```

常用 keycode：

```bash
adb shell input keyevent 3      # HOME
adb shell input keyevent 4      # BACK
adb shell input keyevent 26     # POWER
adb shell input keyevent 82     # MENU
adb shell input keyevent 24     # 音量+
adb shell input keyevent 25     # 音量-
adb shell input keyevent 66     # ENTER
adb shell input keyevent 67     # DELETE
adb shell input keyevent 187    # 最近任务
adb shell input keyevent 224    # 点亮屏幕 / WAKEUP
```

---

## 10. 截图与录屏

### 截图保存到设备

```bash
adb shell screencap /sdcard/screen.png
```

### 拉取截图到电脑

```bash
adb pull /sdcard/screen.png .
```

### 一步截图到电脑

Linux / macOS / PowerShell：

```bash
adb exec-out screencap -p > screen.png
```

### 录屏

```bash
adb shell screenrecord /sdcard/demo.mp4
```

### 限制录屏时间

```bash
adb shell screenrecord --time-limit 10 /sdcard/demo.mp4
```

### 指定录屏分辨率

```bash
adb shell screenrecord --size 720x1280 /sdcard/demo.mp4
```

### 指定录屏码率

```bash
adb shell screenrecord --bit-rate 4000000 /sdcard/demo.mp4
```

---

## 11. 日志 logcat

### 查看实时日志

```bash
adb logcat
```

### 清空日志

```bash
adb logcat -c
```

### 保存日志到文件

```bash
adb logcat > log.txt
```

### 按 tag 过滤

```bash
adb logcat -s MyTag
```

### 按日志级别过滤

```bash
adb logcat *:E
```

日志级别：

```text
V = Verbose
D = Debug
I = Info
W = Warning
E = Error
F = Fatal
S = Silent
```

### 查看崩溃日志

```bash
adb logcat *:E
```

或：

```bash
adb logcat | grep -i "fatal exception"
```

### 指定进程日志

先查 PID：

```bash
adb shell pidof <package_name>
```

再过滤：

```bash
adb logcat --pid=<pid>
```

---

## 12. dumpsys 系统信息

### 查看所有系统服务

```bash
adb shell dumpsys -l
```

### 电池信息

```bash
adb shell dumpsys battery
```

### 恢复真实电池状态

```bash
adb shell dumpsys battery reset
```

### 模拟充电状态

```bash
adb shell dumpsys battery set status 2
```

### 模拟电量

```bash
adb shell dumpsys battery set level 50
```

### 查看内存

```bash
adb shell dumpsys meminfo
adb shell dumpsys meminfo <package_name>
```

### 查看 CPU

```bash
adb shell dumpsys cpuinfo
```

### 查看 Activity

```bash
adb shell dumpsys activity
```

### 查看当前前台 Activity

```bash
adb shell dumpsys activity activities | grep mResumedActivity
```

或：

```bash
adb shell dumpsys window | grep mCurrentFocus
```

### 查看窗口信息

```bash
adb shell dumpsys window
```

### 查看 SurfaceFlinger

```bash
adb shell dumpsys SurfaceFlinger
```

### 查看 Wi-Fi

```bash
adb shell dumpsys wifi
```

### 查看通知

```bash
adb shell dumpsys notification
```

### 查看 alarm

```bash
adb shell dumpsys alarm
```

---

## 13. 设备信息查询

### Android 版本

```bash
adb shell getprop ro.build.version.release
```

### SDK 版本

```bash
adb shell getprop ro.build.version.sdk
```

### 设备型号

```bash
adb shell getprop ro.product.model
```

### 品牌

```bash
adb shell getprop ro.product.brand
```

### CPU 架构

```bash
adb shell getprop ro.product.cpu.abi
```

### 查看所有属性

```bash
adb shell getprop
```

### 查看设备序列号

```bash
adb get-serialno
```

### 查看屏幕分辨率

```bash
adb shell wm size
```

### 查看屏幕密度

```bash
adb shell wm density
```

### 修改分辨率

```bash
adb shell wm size 1080x1920
```

### 恢复分辨率

```bash
adb shell wm size reset
```

### 修改 DPI

```bash
adb shell wm density 420
```

### 恢复 DPI

```bash
adb shell wm density reset
```

---

## 14. 网络相关

### 查看 IP / 路由

```bash
adb shell ip addr
adb shell ip route
```

### ping 测试

```bash
adb shell ping www.baidu.com
```

### 查看网络连接

```bash
adb shell netstat
```

部分新系统可用：

```bash
adb shell ss
```

### 开关 Wi-Fi

```bash
adb shell svc wifi enable
adb shell svc wifi disable
```

### 开关移动数据

```bash
adb shell svc data enable
adb shell svc data disable
```

### 开关飞行模式

开启：

```bash
adb shell settings put global airplane_mode_on 1
adb shell am broadcast -a android.intent.action.AIRPLANE_MODE --ez state true
```

关闭：

```bash
adb shell settings put global airplane_mode_on 0
adb shell am broadcast -a android.intent.action.AIRPLANE_MODE --ez state false
```

---

## 15. 端口转发

### 本地端口转发到设备端口

```bash
adb forward tcp:<local_port> tcp:<device_port>
```

示例：

```bash
adb forward tcp:8080 tcp:8080
```

### 查看 forward 列表

```bash
adb forward --list
```

### 移除指定 forward

```bash
adb forward --remove tcp:8080
```

### 移除全部 forward

```bash
adb forward --remove-all
```

### 反向端口转发

```bash
adb reverse tcp:<device_port> tcp:<local_port>
```

示例：

```bash
adb reverse tcp:8081 tcp:8081
```

### 查看 reverse 列表

```bash
adb reverse --list
```

### 移除 reverse

```bash
adb reverse --remove tcp:8081
adb reverse --remove-all
```

---

## 16. Bugreport / 诊断

### 生成 bugreport

```bash
adb bugreport
```

### 保存 bugreport 到文件

```bash
adb bugreport bugreport.zip
```

### 查看系统启动时间

```bash
adb shell uptime
```

### 查看内核日志，部分设备需要 root

```bash
adb shell dmesg
```

### 查看 tombstones

```bash
adb shell ls /data/tombstones
```

需要权限时：

```bash
adb root
adb pull /data/tombstones .
```

---

## 17. 进程与性能

### 查看进程

```bash
adb shell ps
adb shell ps -A
```

### 查指定应用 PID

```bash
adb shell pidof <package_name>
```

### 杀进程

```bash
adb shell kill <pid>
```

### 查看 top

```bash
adb shell top
```

### 查看指定应用内存

```bash
adb shell dumpsys meminfo <package_name>
```

### 查看指定进程线程

```bash
adb shell ps -T -p <pid>
```

---

## 18. settings 系统设置

Android 设置分为三类：

```text
system
secure
global
```

### 查看设置列表

```bash
adb shell settings list system
adb shell settings list secure
adb shell settings list global
```

### 读取设置

```bash
adb shell settings get system screen_brightness
```

### 修改设置

```bash
adb shell settings put system screen_brightness 100
```

### 删除设置

```bash
adb shell settings delete system <key>
```

### 设置屏幕亮度

```bash
adb shell settings put system screen_brightness 120
```

### 关闭自动亮度

```bash
adb shell settings put system screen_brightness_mode 0
```

### 设置熄屏时间

```bash
adb shell settings put system screen_off_timeout 600000
```

### 充电时保持唤醒

```bash
adb shell settings put global stay_on_while_plugged_in 3
```

---

## 19. 常亮 / 不息屏设置

### 充电时保持屏幕常亮

```bash
adb shell settings put global stay_on_while_plugged_in 3
```

含义：

```text
0 = 关闭
1 = 仅 AC 充电器
2 = 仅 USB 充电
3 = AC + USB 都保持唤醒
```

### 恢复默认

```bash
adb shell settings put global stay_on_while_plugged_in 0
```

### 设置超长自动息屏时间，例如 24 小时

```bash
adb shell settings put system screen_off_timeout 86400000
```

单位是毫秒：

```text
60000    = 1 分钟
300000   = 5 分钟
600000   = 10 分钟
1800000  = 30 分钟
86400000 = 24 小时
```

### 查看当前息屏时间

```bash
adb shell settings get system screen_off_timeout
```

### 恢复为 1 分钟息屏

```bash
adb shell settings put system screen_off_timeout 60000
```

### 立刻点亮屏幕

```bash
adb shell input keyevent 224
```

或使用电源键切换：

```bash
adb shell input keyevent 26
```

### 推荐组合：USB 调试时保持常亮

```bash
adb shell settings put global stay_on_while_plugged_in 3
adb shell settings put system screen_off_timeout 86400000
```

### 恢复组合

```bash
adb shell settings put global stay_on_while_plugged_in 0
adb shell settings put system screen_off_timeout 60000
```

### 重启后是否失效？

通常不会失效，因为这些配置写入了系统 settings 数据库。

重启后可以检查：

```bash
adb shell settings get global stay_on_while_plugged_in
adb shell settings get system screen_off_timeout
```

如果返回：

```text
3
86400000
```

说明设置仍然生效。

> 注意：部分厂商 ROM、省电策略、企业管控系统可能会在开机后重置这些配置。

---

## 20. 电源与重启

### 重启设备

```bash
adb reboot
```

### 重启到 bootloader

```bash
adb reboot bootloader
```

### 重启到 recovery

```bash
adb reboot recovery
```

### 关机

```bash
adb shell reboot -p
```

### 模拟电源键

```bash
adb shell input keyevent 26
```

---

## 21. Fastboot 相关

### 进入 bootloader

```bash
adb reboot bootloader
```

### 查看 fastboot 设备

```bash
fastboot devices
```

### fastboot 重启

```bash
fastboot reboot
```

### 刷入镜像，危险操作

```bash
fastboot flash boot boot.img
fastboot flash recovery recovery.img
fastboot flash system system.img
```

### 解锁 bootloader，危险，会清数据

```bash
fastboot oem unlock
```

或：

```bash
fastboot flashing unlock
```

> ⚠️ 解锁 bootloader 通常会清空设备数据，请谨慎操作。

---

## 22. SQLite 数据库

### 进入 shell

```bash
adb shell
```

### 使用 run-as 进入应用数据库目录

通常需要应用 `debuggable=true`：

```bash
run-as <package_name>
cd /data/data/<package_name>/databases
sqlite3 database.db
```

### 拉取数据库

```bash
adb shell run-as <package_name> cp /data/data/<package_name>/databases/app.db /sdcard/app.db
adb pull /sdcard/app.db .
```

---

## 23. run-as 调试应用数据

适用于 `debuggable=true` 的应用。

### 进入应用私有目录

```bash
adb shell run-as <package_name>
```

### 查看应用私有目录

```bash
adb shell run-as <package_name> ls
```

### 复制私有文件到 sdcard

```bash
adb shell run-as <package_name> cp files/test.txt /sdcard/test.txt
adb pull /sdcard/test.txt .
```

---

## 24. UI 自动化辅助

### 获取当前界面 XML

```bash
adb shell uiautomator dump /sdcard/window.xml
adb pull /sdcard/window.xml .
```

### 查看当前焦点

```bash
adb shell dumpsys window | grep mCurrentFocus
```

### 查看 Activity 栈

```bash
adb shell dumpsys activity activities
```

### monkey 压测

```bash
adb shell monkey -p <package_name> 1000
```

### 带随机种子的 monkey 压测

```bash
adb shell monkey -p <package_name> -s 12345 1000
```

---

## 25. 剪贴板

ADB 原生对 Android 剪贴板支持有限，不同系统差异较大。

### Android 旧版本 / 安装 clipper 工具后可用

```bash
adb shell am broadcast -a clipper.set -e text "hello"
```

> Android 10+ 建议通过输入法、测试工具或自动化框架处理剪贴板。

---

## 26. 模拟 GPS / 定位

通常需要在开发者选项中开启 mock location，并配合 App 使用。

### 模拟器设置定位

```bash
adb emu geo fix <longitude> <latitude>
```

示例：

```bash
adb emu geo fix 116.397 39.908
```

---

## 27. 模拟器 adb emu

仅 Android Emulator 可用。

### 关闭模拟器

```bash
adb emu kill
```

### 查看 AVD 名称

```bash
adb emu avd name
```

### 设置定位

```bash
adb emu geo fix 116.397 39.908
```

### 模拟短信

```bash
adb emu sms send 10086 "hello"
```

### 模拟来电

```bash
adb emu gsm call 10086
```

### 挂断来电

```bash
adb emu gsm cancel 10086
```

---

## 28. 权限与 AppOps

### 查看 appops

```bash
adb shell appops get <package_name>
```

### 设置允许

```bash
adb shell appops set <package_name> <OP_NAME> allow
```

### 设置拒绝

```bash
adb shell appops set <package_name> <OP_NAME> deny
```

### 示例：允许后台定位

```bash
adb shell appops set <package_name> ACCESS_BACKGROUND_LOCATION allow
```

---

## 29. 存储相关

### 查看磁盘空间

```bash
adb shell df -h
```

### 查看目录大小

```bash
adb shell du -h /sdcard
```

### 查看挂载信息

```bash
adb shell mount
```

### 查看外部存储

```bash
adb shell ls /sdcard
adb shell ls /storage/emulated/0
```

---

## 30. 账号、用户、多用户

### 查看用户

```bash
adb shell pm list users
```

### 创建用户

```bash
adb shell pm create-user <name>
```

### 切换用户

```bash
adb shell am switch-user <user_id>
```

### 删除用户

```bash
adb shell pm remove-user <user_id>
```

---

## 31. 常用组合命令

### 查找包名

```bash
adb shell pm list packages | grep appname
```

Windows CMD：

```cmd
adb shell pm list packages | findstr appname
```

### 查看当前 Activity

```bash
adb shell dumpsys window | grep mCurrentFocus
```

### 清数据并重启 App

```bash
adb shell pm clear <package_name>
adb shell monkey -p <package_name> -c android.intent.category.LAUNCHER 1
```

### 安装并启动 App

```bash
adb install -r app.apk
adb shell monkey -p <package_name> -c android.intent.category.LAUNCHER 1
```

### 抓日志并复现问题

```bash
adb logcat -c
adb logcat > crash.log
```

### 截图到电脑

```bash
adb exec-out screencap -p > screen.png
```

### 获取已安装应用 APK

```bash
adb shell pm path <package_name>
adb pull <apk_path> .
```

---

## 32. 常见问题处理

### 设备 unauthorized

```bash
adb kill-server
adb start-server
adb devices
```

然后在手机上确认 USB 调试授权。

如果仍然不行：

```bash
rm ~/.android/adbkey*
adb kill-server
adb start-server
```

重新插拔设备并再次授权。

### device offline

```bash
adb reconnect offline
adb kill-server
adb start-server
```

### 找不到设备

```bash
adb devices
lsusb
```

Linux 可能需要配置 udev 规则。

### 安装失败：version downgrade

```bash
adb install -r -d app.apk
```

### 安装失败：INSTALL_FAILED_UPDATE_INCOMPATIBLE

通常是签名不一致，需要卸载旧版：

```bash
adb uninstall <package_name>
adb install app.apk
```

### permission denied

可能需要：

```bash
adb root
adb remount
```

或者应用必须是 `debuggable=true` 才能使用 `run-as`。

---

## 33. 最常用 20 条

```bash
adb devices
adb shell
adb install -r app.apk
adb uninstall <package>
adb shell pm list packages
adb shell pm clear <package>
adb shell am force-stop <package>
adb shell monkey -p <package> -c android.intent.category.LAUNCHER 1
adb logcat
adb logcat -c
adb pull /sdcard/file .
adb push file /sdcard/
adb exec-out screencap -p > screen.png
adb shell screenrecord /sdcard/demo.mp4
adb shell input tap 500 1000
adb shell input swipe 500 1500 500 500 300
adb shell input text "hello%sworld"
adb shell input keyevent 4
adb shell dumpsys window | grep mCurrentFocus
adb reboot
```

---

## 附录：快速常亮配置

如果设备一直连着 USB 调试，直接执行：

```bash
adb shell settings put global stay_on_while_plugged_in 3
adb shell settings put system screen_off_timeout 86400000
```

验证：

```bash
adb shell settings get global stay_on_while_plugged_in
adb shell settings get system screen_off_timeout
```

恢复：

```bash
adb shell settings put global stay_on_while_plugged_in 0
adb shell settings put system screen_off_timeout 60000
```
