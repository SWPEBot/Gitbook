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
```

## Logcat

### 抓取应用实时日志
```bash
adb logcat --pid=$(adb shell pidof com.google.android.factory.factory) -v color
```

### 清空Logcat日志
```bash
adb logcat -c
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
```

### 获取屏幕分辨率
```bash
adb shell wm size
```
- Physical size：物理屏幕原始分辨率
- Override size：当前覆盖/修改后的分辨率（如果用户或应用修改过）

### WIFI/BT 调试
```bash
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

### Audio Debug
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

