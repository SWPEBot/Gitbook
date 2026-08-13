# ALOS常用的调试方法

## 获取系统日志
```bash
# on the host use
adb bugreport filename.zip
# on the dut use
bugreportz
默认生成路径:/data/user_de/0/com.android.shell/file/bugreports/xxxxxxxxxxxxx.zip

```
### 获取系统属性
```bash
adb shell getprop

# 查看OS版本
adb shell getprop ro.build.version.desktop_os

# 查看 CPU 信息
adb shell cat /proc/cpuinfo

# 查看内存
adb shell cat /proc/meminfo
adb shell dumpsys meminfo com.example.app    # 应用内存详情

# 查看电池状态
adb shell dumpsys battery

# 查看 CPU 使用率
adb shell top -m 10 -s cpu        # CPU 占用 Top10
adb shell dumpsys cpuinfo         # CPU 详细信息

# 查看网络配置
adb shell ifconfig
adb shell ip addr

```

## Logcat

```bash

# 过滤指定包名（最实用）
adb logcat --pid=$(adb shell pidof -s com.example.app)
adb logcat --pid=$(adb shell pidof com.google.android.factory.factory) -v color

# 实时查看日志
adb logcat

# 过滤指定 TAG
adb logcat -s MyTag:D

# 过滤指定级别以上
adb logcat *:E          # 仅 Error 及以上
adb logcat *:W          # Warning 及以上

# 清空日志缓存
adb logcat -c

# 导出日志到文件
adb logcat -d > log.txt
adb logcat -v threadtime > log.txt   # 带线程和时间

# 查看崩溃日志（crash）
adb logcat -b crash

# 查看内核日志
adb shell dmesg
```

### 恢复出厂设置
```bash
adb shell cmd recovery wipe
```
## scrcpy (screen copy) 实时控制Android设备屏幕

### 录制屏幕
```bash
scrcpy --record=file.mp4
```

### 关屏控制
```bash
scrcpy --turn-screen-off
```

### 获取无法确认的包名
```bash
adb shell dumpsys window | grep mCurrentFocus
output:mCurrentFocus=Window{f0d842c u10 com.google.android.factory.factory/com.google.android.factory.factory.FactoryMainActivity}

通过关键词搜寻
adb shell pm list packages | grep -i 'terminal'
```

### 禁用应用
```bash
adb shell pm disable-user --user 10 com.google.android.googlequicksearchbox
```

### 启动应用
```bash
adb shell pm enable --user 10 com.google.android.factory.factory
```

### 清除数据
```bash
adb shell pm clear --user 10 com.google.android.factory.factory

rm -rf /data/user_de/10/com.google.android.factory.factory/file/xxxxx

# 导出应用 APK
adb shell pm path com.example.app
adb pull /data/app/xxx/base.apk ./app.apk
```

### 模拟输入(自动化测试必用)
```bash
# 点击屏幕
adb shell input tap 500 800

# 滑动屏幕
adb shell input swipe 300 1000 300 500    # 从下到上滑动
adb shell input swipe 300 500 300 500 2000 # 长按 2 秒

# 输入文字
adb shell input text "HelloWorld"

# 模拟按键
adb shell input keyevent 3     # HOME 键
adb shell input keyevent 4     # 返回键
adb shell input keyevent 24    # 音量+
adb shell input keyevent 25    # 音量-
adb shell input keyevent 26    # 电源键
adb shell input keyevent 82    # 菜单键

```

### WIFI/BT 调试
```bash
# 查看 Wi-Fi 信息
adb shell dumpsys wifi

# 查看网络连接
adb shell netstat

# 开启WIFI
adb shell cmd wifi set-wifi-enabled enabled

# 扫描WIFI
adb shell cmd wifi start-scan

# 查看搜索结果
adb shell cmd wifi list-scan-results

# 查看蓝牙信息
adb shell dumpsys bluetooth_manager

# 重启蓝牙生效
adb shell svc bluetooth disable
adb shell svc bluetooth enable
```
### 屏幕操作
```bash
# 获取分辨率
adb shell wm size

# 截图
adb shell screencap /sdcard/screen.png
adb pull /sdcard/screen.png ./screen.png

# 录屏（默认 3 分钟上限）
adb shell screenrecord /sdcard/video.mp4
adb shell screenrecord --time-limit 10 /sdcard/video.mp4   # 录 10 秒
adb shell screenrecord --size 720x1280 /sdcard/video.mp4   # 指定分辨率
adb shell screenrecord --bit-rate 4000000 /sdcard/video.mp4 # 4Mbps 码率

# 修改屏幕分辨率（调试适配）
adb shell wm size 1080x1920
adb shell wm size reset        # 恢复默认

# 修改 DPI
adb shell wm density 320
adb shell wm density reset
```
### 文件传输
```bash
# 推送到设备
adb push local.txt /sdcard/

# 从设备拉取
adb pull /sdcard/file.txt ./
adb pull /data/data/com.example.app/databases/ ./db/   # 需要 root

# 查看设备存储
adb shell df -h
adb shell ls -la /sdcard/
```

### Audio调试
```bash
# 列出 ALSA 设备
adb shell cat /proc/asound/cards
adb shell cat /proc/asound/devices

# 查看声卡信息
adb shell dras_tool mixer-path-test

# 录制10秒音频
adb shell dras_tool arecord --duration-sec 10 /sdcard/rec.wav

# 播放音频
adb shell dras_tool aplay /sdcard/rec.wav

```

### 无线调试
```bash
# 方式1：配对码方式（无需 USB）
adb pair 192.168.1.100:42073      # 输入配对码
adb connect 192.168.1.100:39111   # 连接

# 方式2：传统方式（需先插 USB）
adb tcpip 5555
adb connect 192.168.1.100:5555
```

