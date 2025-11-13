# Android Studio User Guide


**Linux**
注意：目前不支持采用 ARM CPU 的 Linux 计算机。
Linux 的系统要求如下：

- OS	任何支持 GNOME、KDE 或 Unity DE 的 64 位 Linux 发行版；GNU C 库 (glibc) 2.31 或更高版本。	最新 64 位版本的 Linux
- RAM	Studio：8 GB Studio 和模拟器：16 GB	至少有 32 GB 的 RAM
- CPU	必须支持虚拟化（Intel VT-x 或 AMD-V，在 BIOS 中启用）。

## 如需在 Linux 上安装 Android Studio，请按以下步骤操作：

将您下载的 .tar.gz 文件解压缩到您应用的相应位置，例如 /usr/local/ 中（用于用户个人资料）或者 /opt/ 中（用于共享用户）。
对于 64 位版本的 Linux，请先安装 64 位计算机所需的库。

如要启动 Android Studio，请打开一个终端，进入 android-studio/bin/ 目录，并执行 studio。
选择是否想要导入之前的 Android Studio 设置，然后点击 OK。
Android Studio 设置向导会引导您完成其余设置，其中包括下载开发所需的 Android SDK 组件。
提示：如需使 Android Studio 出现在您的应用列表中，请从 Android Studio 菜单栏中依次选择 Tools > Create Desktop Entry。

64 位计算机所需的库 
如果您运行的是 64 位版本的 Ubuntu，那么您需要使用以下命令安装一些 32 位库：
```sh
sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386
```
如果您运行的是 64 位版本的 Fedora，则所用命令为：
```sh
sudo yum install zlib.i686 ncurses-libs.i686 bzip2-libs.i686
```


## 安装虚拟机运行时出现错误The emulator process for AVD Medium_Tablet has terminated  
```sh
cd ~/.android/avd/{device_name}/config.ini
加入如下内容 
hw.gpu.enabled=no
hw.gpu.mode=off
```
如果这样能启动 → 说明是 GPU/OpenGL 问题


