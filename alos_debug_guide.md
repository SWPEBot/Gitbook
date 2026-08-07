# ALOS常用的调试方法

## Get system log
```bash
# on the host use
adb bugreport filename.zip
# on the dut use
bugreportz
默认生成路径:/data/user_de/0/com.android.shell/file/bugreports/xxxxxxxxxxxxx.zip
```
## Logcat

### 抓取factory日志
```bash
adb logcat --pid=$(adb shell pidof com.google.android.factory.factory) -v color
```

### 清空Logcat日志
```bash
adb logcat -c
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
output:mCurrentFocus=Window{f0d842c u10 com.google.android.factory.factory/``com.google.android.factory.factory.Factory``MainActivity}
```

