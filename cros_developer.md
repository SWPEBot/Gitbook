# Chrome OS 开发人员指南

## 参考链接

* [Chrome OS Developer Guide](https://chromium.googlesource.com/chromiumos/docs/+/master/developer_guide.md)
* [ChromiumOS Factory Platform](https://chromium.googlesource.com/chromiumos/platform/factory/)

## Chrome OS 开发环境设置

### 先决条件

* ~~Ubuntu Linux (version == 16.04.6 Xenial Xerus)~~
  * ~~这是唯一的官方支持发行版~~
  * ~~在Debian 发行版及其他 Kernel > 2.6.16+ Linux 依然可工作，但不推荐~~
* Ubuntu Linux (version == 18.04.4 Bionic Beaver)
  * 因 Google 当前正在迁移 Python 3.6, Ubuntu 16.04 默认 Python3 为 Python3.5,
    且 Ubuntu 18.04 为默认使用 Python3.6, 为避免由环境造成额外的问题，故推荐使用
    Ubuntu 18.04 LTS
  * **基于桌面环境及软件源问题，推荐使用 Linux Mint 19.3 (Recommand)**
  * 在Debian 发行版及其他 Kernel > 2.6.16+ Linux 依然可工作，但不推荐
* 用于执行构建的 64 位系统
  * 不支持 32 位
* 具有 sudo 权限的用户帐户
  * 验证 sudo 权限: 运行 `sudo ls -la /root` 并确保输入密码后无异常提示.
* 大于或等于 4 GB RAM
  * 推荐使用大于等于 8GB RAM.
* 存储 128G 起
  * 保证正常的工作使用, 及 make master ... 推荐存储大于等于 500G.

### 安装所需 Packages

```bash
sudo apt-get install git git-core gitk git-gui curl lvm2 thin-provisioning-tools \
     python-pkg-resources python-virtualenv python-oauth2client xz-utils \
     tmux vim tree minicom python-pip python3-pip
```

### 安装 depot_tools

```bash
# 通常会将 depot_tools 存放在${HOME}下以保证多环境变量配置
cd ${HOME}
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
```

### 设置环境变量

* 确保将 `depot_tools` 添加到 system env path.
* 编辑 `${HOME}` 下的 `.bashrc` 或 `.zshrc` 文件以设置系统环境变量.
* 也可以使用如下命令添加环境变量

  ```bash
  echo 'export PATH=`pwd`/depot_tools:"$PATH"' >> ~/.bashrc
  ```

  如果使用的是 `zsh`, 请使用如下命令：

  ```bash
  echo 'export PATH=`pwd`/depot_tools:"$PATH"' >> ~/.zshrc
  ```

* 重启 `bash` 或 `zsh` env 使环境变量生效

  ```bash
  source ~/.bashrc
  source ~/.zshrc
  ```

### 配置 git 账户

```bash
git config --global user.email "you@quanta.corp-partner.google.com"
git config --global user.name "Your Name"
```

### Gerrit 账户设置

1. 設置Chromium Gerrit Git repositories認證凭据, 步驟如下：
    *  訪問 <https://chromium-review.googlesource.com/settings/#EmailAddresses>
    *  使用用于 git 提交的电子邮件登录, 通常为 "you@quanta.corp-partner.google.com" 账户
    *  根据平台复制文本框的内容到 Terminal後按Enter 鍵即可．
    *  验证：运行`git ls-remote https://chromium.googlesource.com/chromiumos/manifest.git`
        > 命令应该不会提示输入任何凭据, 应该只是打印出的 git 分支列表

2. 設置Chrome Internal Gerrit Git repositories認證凭据, 步驟如下：
    *  訪問 <https://chrome-internal-review.googlesource.com/settings/#EmailAddresses>
    *  使用用于 git 提交的电子邮件登录, 通常为 "you@quanta.corp-partner.google.com" 账户
    *  根据平台复制文本框的内容到 Terminal後按Enter 鍵即可．
    *  验证：run`git ls-remote https://chrome-internal.googlesource.com/<REPO_PATH>.git`
        > 例如: git ls-remote https://chrome-internal.googlesource.com/chromeos/overlays/chromeos-partner-overlay.git
        > 執行該命令後应该不会提示输入任何凭据, 应该只是打印出的 git 分支列表
    *  参考链接[Gerrit Guide](http://www.chromium.org/chromium-os/developer-guide/gerrit-guide)

3. 确保姓名设置正确
    * 访问 <https://chromium-review.googlesource.com/#/settings/> 并查看“全名”字段
    * 如果未设置, 则需要更新您的 Google+ 个人资料
    * 在 Google+ 个人资料更新后, 需要退出并重新登录 Gerrit

4. 分支构建

    * 参考链接: [Working on branch](https://sites.google.com/a/chromium.org/dev/chromium-os/how-tos-and-troubleshooting/working-on-a-branch)

### 再次确认系统架构是否为 x86_64 体系

```bash
# 仔细检查你是否正在运行64位体系结构的操作系统
uname -m
```

### 创建一个文件夹以存储 ChromiumOS Source

* TOT 构建

  ```bash
  mkdir -p ${HOME}/chromiumos
  ```

* 分支构建

  * 获取分支名称可以在 [chromium googlesource](https://chromium.googlesource.com/chromiumos/platform/factory) 查看你项目的 `factory branch`
  * 请将 `chromiumos` 替换为分支名称以分类示例：

    ```bash
    mkdir -p ${HOME}/factory-board-10000.B
    ```

### 使用 repo 获取 Chromium Source Code

* 获取 TOT Public Source

  ```bash
  cd ${HOME}/chromiumos
  repo init -u https://chromium.googlesource.com/chromiumos/manifest.git --repo-url https://chromium.googlesource.com/external/repo.git
  repo sync -j4
  ```

* 获取 `factory branch` Private Source

  ```bash
  cd ${HOME}/factory-board-1xxxx.B
  repo init -u https://chromium.googlesource.com/chromiumos/manifest.git --repo-url https://chromium.googlesource.com/external/repo.git -b 'factory-board-1xxxx.B'
  repo sync -j4
  ```

  示例：

  ```bash
  cd ${HOME}/factory-nami-10715.B
  repo init -u https://chromium.googlesource.com/chromiumos/manifest.git --repo-url https://chromium.googlesource.com/external/repo.git -b 'factory-nami-10715.B'
  repo sync -j4
  ```

  _注意： -j4 告诉 repo 同时同步最多4个存储库, 您可以根据互联网连接的速度调整数量, 对于初始同步, 通常要求您使用不超过8个并发作业.
  (对于以后的同步, 当你已经拥有大部分源本地时, 使用 -j8 左右通常都可以)_

### 配置 `local_manifest`

1. 在`factory-board-1xxxx.B` 或 `chromiumos` 目录中, 进入 `.repo` 目录, 创建`local_manifests`文件夹,
   并在该文件夹中创建`local_manifest.xml`文件

    示例：

    ```bash
    cd factory-board-1xxxx.B
    mkdir -p .repo/local_manifests
    touch .repo/local_manifests/local_manifest.xml
    ```

2. 完成上面 `Gerrit 账户设置`
3. 在 `local_manifest.xml` 中添加 `Project Source Url`

    对应权限可在 [Git repositories on chrome-internal](https://chrome-internal.googlesource.com/) 中查看

    示例：

    ```xml
    <manifest>
      <!-- Build toolkit only,such as minilayout -->
      <remote name="cros-internal"
              fetch="https://chrome-internal.googlesource.com"
              review="https://chrome-internal-review.googlesource.com"/>

      <project remote="cros-internal"
               path="src/private-overlays/chromeos-partner-overlay"
               groups="minilayout"
               name="chromeos/overlays/chromeos-partner-overlay"/>

      <project remote="cros-internal"
               path="src/private-overlays/overlay-puff-private"
               groups="minilayout"
               name="chromeos/overlays/overlay-puff-private"/>

      <!-- Build test image or factory shim must add the repo url as below -->
      <project remote="cros-internal"
               path="src/private-overlays/baseboard-puff-private"
               groups="minilayout"
               name="chromeos/overlays/baseboard-puff-private"/>

      <project remote="cros-internal"
               path="src/private-overlays/chipset-cml-private"
               groups="minilayout"
               name="chromeos/overlays/chipset-cml-private"/>

      <project remote="cros-internal"
               path="src/platform/cheets-scripts"
               groups="minilayout"
               name="chromeos/cheets-scripts"/>

      <project remote="cros-internal"
               path="src/private-overlays/project-cheets-private"
               groups="minilayout"
               name="chromeos/overlays/project-cheets-private"/>

      <project remote="cros-internal"
               path="src/third_party/autotest-tests-cheets"
               groups="minilayout"
               name="chromeos/autotest-cheets"/>
    </manifest>
    ```

4. 重新运行`repo sync -j4`, 获取 Private Overlay Source Code.
5. 等待同步 Source Code 完成

### 安装 Cros SDK(进入 Chroot)

```bash
cd ${HOME}/chromiumos # example: cd ${HOME}/factory-nami-10715.B
cros_sdk
```

* Chroot 环境安装常用工具

  ```bash
  sudo emerge tmux tree minicom
  ```

### Setup Board

Initialize the build for a board
you should be in the `~/trunk/src/scripts` directory:

```bash
setup_board --board=${BOARD}
```

示例:

> 过程中可能会提示缺失 Overlay，请根据提示添加

```bash
cros_sdk
setup_board --board=hatch
# 如提示异常，请使用如下命令
./setup_board --board=hatch --force
```

## Chrome OS Factory Toolkit

### Build Factory Toolkit

> 首次 Build
> 需完成上述环境配置，且在 Chroot 环境下

```bash
cros_workon --board $BOARD start factory
emerge-$BOARD factory-board factory --getbinpkg -j 16
```

> 注: toolkit 将会生成在 `/build/$BOARD/usr/local/factory/bundle/toolkit/` 目录,
> 如: `/build/hatch/usr/local/factory/bundle/toolkit/install_factory_toolkit.run`

### Re-Build Factory Toolkit

* Method 1:

  使用 emerge 构建

  ```bash
  emerge-$BOARD factory
  # Toolkit 将会生成在 `/build/$BOARD/usr/local/factory/bundle/toolkit/install_factory_toolkit.run`
  ```

  示例:
  修改 `~/trunk/src/platform/factory/py/test/pytests/` 中部分文件，重新打包 Toolkit

  ```bash
  emerge-hatch factory
  ```

* Method 2:

  不推荐

  ```bash
  emerge-$BOARD factory-board
  # 需要在 factory repo 目录进行构建
  cd ~/trunk/src/platform/factory
  #make BOARD=hatch toolkit
  make BOARD=$BOARD toolkit
  # toolkit 将会生成在当前目录 build/ 目录下
  # 如：./build/install_factory_toolkit.run
  ```

### 本地修改 Toolkit

1. Unpack toolkit

    ```bash
    toolkit/install_factory_toolkit.run --target /tmp/unpack_source --noexec
    ```

    > 将 Toolkit 解压缩到 unpack_source 文件夹，你可以编辑它里面的内容

2. ~~Repack Toolkit~~

    ```bash
    #./tmp/unpack_source/usr/local/factory/py/toolkit/installer.py --pack-into ./install_factory_toolkit_xxx.run
    ```

3. Repack Toolkit New

   ```bash
   # Use self repack **Recommand**
   ./install_factory_toolkit.run -- --repack source/ --pack-into ./install_factory_toolkit_new.run

   # Call factory_env repack **Not Recommand**
   #./unpack/usr/local/factory/bin/factory_env ./unpack/usr/local/factory/py/toolkit/installer.py --pack-into ./install_factory_toolkit_new.run
   ```

* ~~使用 root 权限重新打包 Toolkit~~

  ~~对于 dome 系统上传 Toolkit 无法加载 Ui 问题, 解决方案如下：~~

  1. ~~exec:~~

      ```bash
      #./install_factory_toolkit.run –noexec –target abc
      ##No sudo for non root edit, abc is you created folder
      ```

  2. ~~make some changes in abc~~
  3. ~~`chmod –R ugo+rX abc`~~
  4. ~~under chroot~~
  5. ~~`sudo ./abc/usr/local/factory/py/toolkit/installer.py –pack-into new_factory_install_toolkit.run`~~
  6. ~~import in dome and connect dut to update toolkit, OK to load factory UI after reboot.~~

## Building Test Image


完成 Chrome OS 开发环境设置，且完成 Setup Board 步骤

Under chroot, after setting up board, you can get the test image by running following commands in `trunk/src/scripts`:

```bash
./build_packages --board $BOARD
./build_image --board $BOARD test
```

示例一：

```bash
# 注，非 Google 人员构建需同意 Google-TOS licenses
./build_packages --board hatch --accept_licenses Google-TOS
./build_image --board hatch test
```

示例二：
或者配置LICENSE 設定, 就不需要加參數"--accept_licenses Google-TOS" 

```bash
sudo vim /etc/make.conf.user # inside chroot
# 打開後文件末尾增加 'ACCEPT_LICENSE="*", 注意去掉換行時默認增加的註釋符號"#",否則增加的內容會被註釋;
./build_packages --board puff
./build_image --board puff test
```

* 在 test image build 完成后，你可以将它 flash 到 USB

  ```bash
  # inside chroot
  cros flash usb:// path/to/chromiumos_test_image.bin

  # outside chroot
  sudo dd bs=4M if=path/to/image/chromiumos_test_image.bin of=/dev/sdX \
          iflag=fullblock oflag=dsync
  ```

## Building Factory (Install) Shim

完成 Chrome OS 开发环境设置，且完成 Setup Board 步骤

Under chroot, after setting up board, you can get the test image by running following commands in `trunk/src/scripts`:

```bash
./build_packages --board $BOARD
./build_image --board $BOARD factory_install
```

示例：

```bash
./build_packages --board hatch
./build_image --board hatch factory_install
```

* 修改 `dev_image/etc/lsb-factory`

  ```bash
  setup/image_tool edit_lsb -i path/to/factory_install_shim.bin
  ```

* flash 到 USB

  ```bash
  # inside chroot
  cros flash usb:// path/to/factory_install_shim.bin

  # outside chroot
  sudo dd bs=4M if=path/to/image/factory_install_shim.bin of=/dev/sdX \
          iflag=fullblock oflag=dsync
  ```

## 提交 Private Overlay 更改

在 Google 创建工厂分支后, 分支名称通常为 `factory-board-1xxxx.B`, 在大多数情况下, 更改应在私有覆盖中完成.

**如果当前处于网络较差环境，可以更改 `repo init` 使用 `minilayout`,步驟如下**
* 获取 master或者factory branch Private Source

```bash
# 如果是Factory Branch 還沒切出來，那就是使用Master,即修改下面'factory-nami-xxxx.B'爲'master'
repo init -u https://chromium.googlesource.com/chromiumos/manifest.git --repo-url https://chromium.googlesource.com/external/repo.git -b 'factory-nami-xxxx.B' -g minilayout
```

* Setting Private Minilayout Permission

```bash
vim .repo/local_manifests/local_manifest.xml
```

```xml
<manifest>
  <remote name="cros-internal"
    fetch="https://chrome-internal.googlesource.com"
    review="https://chrome-internal-review.googlesource.com"/>

  <project remote="cros-internal"
    path="src/private-overlays/chromeos-partner-overlay"
    groups="minilayout"
    name="chromeos/overlays/chromeos-partner-overlay"/>

  <project remote="cros-internal"
    path="src/private-overlays/overlay-$board-private"
    groups="minilayout"
    name="chromeos/overlays/overlay-$board-private"/>
</manifest>
```

* 如果repo sync 時提示Error"fatal: no revision xxxx.."類似的信息, `local_manifest.xml`需要增加'revision="refs/heads/factory-$board-xxxx.B"'才能解決，否則跳過該步驟， 例如:

```xml
<manifest>
  <remote name="cros-internal"
    fetch="https://chrome-internal.googlesource.com"
    review="https://chrome-internal-review.googlesource.com"/>

  <project remote="cros-internal"
    path="src/private-overlays/chromeos-partner-overlay"
    groups="minilayout"
    revision="refs/heads/factory-$board-xxxx.B"
    name="chromeos/overlays/chromeos-partner-overlay"/>

  <project remote="cros-internal"
    path="src/private-overlays/overlay-$board-private"
    groups="minilayout"
    revision="refs/heads/factory-$board-xxxx.B"
    name="chromeos/overlays/overlay-$board-private"/>
</manifest>
```

* 然後在配置Gerrit 账户设置, 具體步驟請參考本業上面`Gerrit 账户设置`具體方法

* 运行`repo sync -j4`去获取 Private Overlay Source Code.

* 安装 Cros SDK(进入 Chroot)

path: `~/trunk/src/private-overlays/overlay-$BOARD-private/chromeos-base/factory-board/files`

**Use the following command to upload a private overlay change**

```bash
# make sure you have the latest source
repo sync .
# 創建本地分支後，才能去修改要上code
git checkout -b 'local-branch-name' 'cros-internal/factory-nami-10715.B'
# 修改code 後然後增加改變到緩存倉庫
git add changes
# Commit 務必请根据 Google 模板填写
git commit 
#repo upload . -t --cbr --no-verify    # --no-verify means don't run pre-sumbit checks, deprecated.
repo upload . -t --cbr 
```

如果成功提交了更改, 与您的更改关联的链接将显示在屏幕上, 
e.g.<https://chrome-internal-review.googlesource.com/#/c/1212345/>.

然後在浏览器中打开链接, 并添加Google Factory Team 軟體工程師賬戶为Reviewers, 
並在cc(抄送)欄位加Google SIE/PM 和Quanta PM. 然後code-Review/Verified/Auto-Sunmit/Commit-Queue往最大數字選．
上述步驟完成，即完成CL 提交．

### 修改已提交的 CL

1. 在本地检查更改并对文件进行所需更改
2. 使用以下命令更新本地提交以及更改

    ```bash
    #git add . && git commit --amend --no-edit
    git add . && git commit --amend
    ```

    保留提交消息的“Change-ID”

3. 将更新推送到 gerrit

    ```bash
    #repo upload . -t --cbr --no-verify
    repo upload . -t --cbr
    ```

### Update Public Change(谨慎使用)

完成 Chrome OS 开发环境设置

```bash
cd ~/trunk/src/platform/factory/
git status # check local files status
repo start path-local-branch # start a local branch
# make some changes
git add changes_file
git commit ...
repo upload --no-verify
#git checkout m/factory-branch-xxx.b # checkout to main branch
```

### 检查项目的分支

```bash
# 在chromiumos/src/private-overlays/overlay-$BOARD-private/ 目录下
# 显示现在使用的分支的详细信息
  git branch -vv
# 显示所有的分支信息
  git branch -a
# 切换分支
  git checkout branch-name
```

## Run Goofy in Docker

通常会在没有实体机的情况下，你可以在 `Docker` 中安装 `Test Image`

* 安装 Docker

  ```bash
  sudo apt-get inatll docker.io

  # 将用户添加到 Docker用户组,这样做可以让您运行 Docker 命令，而无需每次都调用sudo
  sudo usermod -aG docker ${USER}
  ```

* 安装 Test Image 实例

  ```bash
  ./setup/image_tool docker -i PATH_TO/chromiumos_test_image.bin
  ```

  * 或者安装 factory image

    ```bash
    ./install_factory_toolkit.run PATH_TO/chromiumos_factory_image.bin
    ./setup/image_tool docker -i PATH_TO/chromiumos_factory_image.bin
    ```

* Run Goofy in Docker

  * 检查安装的 images

    ```bash
    sudo docker images
    ```

  * Run Docker Image

  * 使用 goofy shell Run Docker Image

    ```bash
    setup/cros_docker.sh goofy shell
    # 会进入 test image shell console

    ```

* 安装 toolkit 到 test image

  ```bash
  cp install_factory_toolkit.run /cros_docker
  setup/cros_docker.sh goofy shell
  /mnt/install_factory_toolkit.run
    # 使用下面命令开启 goofy docker，默认端口为4012，你可以使用浏览器打开IP地址加端口进行连接
  /usr/local/factory/bin/goofy_docker
    # 在浏览器中输入：即可显示toolkit UI 
  localhost:4012
  ```
