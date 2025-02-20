# Chrome OS 开发人员快速指南

## 参考链接

* [Chromium OS Quick Start Guide](https://www.chromium.org/chromium-os/developer-library/guides/development/developer-guide/)

> 注: 该文档仅供 Public Source 使用

## Chrome OS 开发环境设置

### 先决条件
* Ubuntu Linux（版本 22.04 - Jammy）
  * 大多数使用 ChromiumOS 的开发人员都在使用 Jammy（Ubuntu 的 22.04 LTS 版本）和 Debian 测试 （Trixie）。如果您运行的是不同的 Linux 发行版，事情可能会奏效，但如果您使用的是其中之一，您可能会发现生活会更轻松。

* 用于执行构建的 x86_64 64 位系统

* 具有 sudo 访问权限的帐户
  * 您需要 root 访问权限才能运行 chroot 命令并修改挂载表。注意：请勿以 root 身份运行本文档中列出的任何命令 - 这些命令本身将在需要时运行 sudo 来获取 root 访问权限。

* 数 GB 的 RAM
  * 根据此线程，截至 2017 年 3 月，链接 Chrome 需要 8 GB 到 28 GB 的 RAM;您也许可以用更少的钱过得去，但代价是构建速度较慢，但具有足够的交换空间。在构建 chromeos-chrome 软件包时，看到类似 error： ld terminated with signal 9 [Killed] 的错误表明您需要更多 RAM。如果您不构建自己的 Chrome 副本，则 RAM 要求将大大降低。

* 良好的互联网连接,这将有助于初始下载（至少约 2 GB）和任何进一步的更新。

### Python
如果您的系统没有安装兼容的 Python 版本，则需要安装 Python 3.8 或更高版本。

要检查你的 Python 版本：
```bash
(outside)
$ python3 -V
```
如果返回错误或低于 3.8 的版本，请继续执行本节的剩余部分。如果没有，请跳过本节。

如果您的系统支持较新的特定 Python 版本，则可以安装它。如果存在不兼容的版本，请将其卸载：
```bash
(outside)
$ sudo apt-get remove <current Python package>
```

并安装新版本：
```bash
(outside)
$ sudo apt-get install python3.9
```

### 安装开发工具
需要一些主机操作系统工具来操作代码、引导开发环境以及稍后运行预上传挂钩。
```bash
(outside)
# On Ubuntu, make sure to enable the universe repository.
$ sudo add-apt-repository universe
$ sudo apt-get install git gitk git-gui curl
```

### 安装 depot_tools

```bash
# 通常会将 depot_tools 存放在${HOME}下以保证多环境配置
cd ${HOME}
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
```

### 设置环境变量

* 确保将 `depot_tools` 添加到 system env path.
* 编辑 `${HOME}` 下的 `.bashrc` 或 `.zshrc` 文件以设置系统环境变量.
* 也可以使用如下命令添加环境变量

  ```bash
  echo 'export PATH=/home/$USER/depot_tools:$PATH' >> ~/.bashrc
  ```

  如果使用的是 `zsh`, 请使用如下命令：

  ```bash
  echo 'export PATH=/home/$USER/depot_tools:$PATH' >> ~/.zshrc
  ```

* 重启 `bash` 或 `zsh` env 使环境变量生效

  ```bash
  source ~/.bashrc
  source ~/.zshrc
  ```

### 设置区域

如果您在已经使用的系统上构建，则可能不需要这些，但是如果您在 GCE 上有一个干净的实例，则需要设置更好的语言环境。例如，在 GCE 上的 Debian 上，请执行以下操作：
```bash
(outside)
$ sudo apt-get install locales
$ sudo dpkg-reconfigure locales
```

### 配置 Git 账户

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

### 检查系统架构是否为 x86_64 体系

```bash
# 仔细检查你是否正在运行64位体系结构的操作系统
uname -m
```

### 创建文件夹以存储 Chromium Source

```bash
mkdir -p ${HOME}/chromiumos
```

### 使用 repo 获取 Chromium Source Code

```bash
(outside)
$ cd ~/chromiumos
$ repo init -u https://chromium.googlesource.com/chromiumos/manifest -b stable
$ repo sync -j4
```

### 安装 Cros SDK（进入 Chroot）

```bash
cd ${HOME}/chromiumos
cros_sdk
# 首次Download SDK tarball 
```

### 构建 hdctools (For Use Servo Flash Firmware)

```bash
# inside chroot
sudo emerge hdctools
sudo emerge tmux tree minicom
```
