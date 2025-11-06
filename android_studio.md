## Android Studio User Guide

安装虚拟机运行时出现错误The emulator process for AVD Medium_Tablet has terminated  
``sh
cd ~/.android/avd/{device_name}/config.ini
加入 
hw.gpu.enabled=no
hw.gpu.mode=off
``
如果这样能启动 → 说明是 GPU/OpenGL 问题


