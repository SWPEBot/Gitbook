## 设置安卓源代码快速指南

1. 安装必要的插件
```bash
sudo apt-get install git-core gnupg flex bison build-essential zip curl zlib1g-dev libc6-dev-i386 x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig
```

2. Generate git cookies

首次，用户需要更新 partner-android 代码库的凭据,请按照以下步骤生成 .gitcookies 文件，以便访问 partner-android 并同步代码.

[android_review_code](https://partner-android-review.googlesource.com/settings/)  enter **Obtain password (opens in a new tab)**

```bash
eval 'set +o history' 2>/dev/null || setopt HIST_IGNORE_SPACE 2>/dev/null
 touch ~/.gitcookies
 chmod 0600 ~/.gitcookies

 git config --global http.cookiefile ~/.gitcookies

 tr , \\t <<\__END__ >>~/.gitcookies
partner-android.googlesource.com,FALSE,/,TRUE,2147483647,o,git-yang.lyn.inventec.corp-partner.google.com=1//0g_qWdMjsOeENCgYIARAAGBASNwF-L9IrWmImCDmnzI8MhySLNCaUiwOvgv_jq5Fs_9CuyaWQjkMxGq18DViC92nsuudiJXvPgYU
partner-android-review.googlesource.com,FALSE,/,TRUE,2147483647,o,git-yang.lyn.inventec.corp-partner.google.com=1//0g_qWdMjsOeENCgYIARAAGBASNwF-L9IrWmImCDmnzI8MhySLNCaUiwOvgv_jq5Fs_9CuyaWQjkMxGq18DViC92nsuudiJXvPgYU
__END__
eval 'set -o history' 2>/dev/null || unsetopt HIST_IGNORE_SPACE 2>/dev/null
```
3. Repo init:
```bash
export BOARD=ocicat
mkdir -p alos/${BOARD} &&  cd alos/${BOARD}
REPO_ALLOW_SHALLOW=0 repo init -c -u https://partner-android.googlesource.com/platform/vendor/pdk/${BOARD}/manifest/ -b ${BOARD}-main-fs -m default_with_gms.xml --partial-clone --partial-clone-exclude=platform/frameworks/base --clone-filter=blob:limit=10M
```
4. ln -sf vendor/google/desktop/dev/kernel/replace_prebuilts.py

```bash
pushd vendor/google_shared/packages/desktop/  ##跳转目录到pdk
git clone -b ${BOARD}-main-fs "https://partner-android.googlesource.com/platform/vendor/unbundled_google/packages/desktop/RepairPrebuilt"
popd
```
5. 完整同步
```bash
repo sync -c -j99  ## 若出现单个路径无法同步 repo sync -c {path}
```
**注意：多线程同步必要有一些文件同步失败,处理方法如下**
- a.修改source code 未submit,再次repo sync 出现checkout fail 
```bash
enter path
git reset --hard
git clean -fd  or git checkout . && git clean -xdf
```
Ex:  repo sync vendor/google_shared/packages/desktop/Factory
- b. 由于多线程拉取代码，导致repo checkout file fail，解决方法如下
```bash
rm -rf .repo/xxxxx/xxxxx/xxxxx.git
repo sync -c xxxxx/xxxxx/xxxxx
```
Ex: repo sync -c external/libtextclassifier -j1 repo sync -c external/libsrtp2 -j1



## ADB连接DUT

```sh
adb devices      #device name PRMFRCGI5XV8INL

adb devices -l      #USB port/product/model/id
#usb:3-4 product:corot model:23078RKD5C device:corot transport_id:4
```
## 通过TCP/IP 连接DUT

要启用通过 TCP 连接 ADB，请设置被测设备 (DUT) 上 GBB 标志的最高有效位 (MSB)（第 31 位或 0x80000000）

设置GBB flags
```sh
$(outside docker) start-servod --channel=release --board=brya -n getting_gbb
$(outside docker) docker exec -it getting_gbb-docker_servod bash
$(inside docker) futility gbb --set --flags +0x80000000 --servo
```
inside DUT
```sh
futility gbb --set --flash --flags +0x80000000
```
On Host
```sh
adb connect <dut-ip-address>
```
进入DUT Shell
```sh
adb shell
```

## 开启开发者模式
要开启开发者模式，您需要：
1. 进入“设置”
2. 选择“关于手机”
3. 向下滚动至“版本号”
4. 连续点击“版本号”七次

开启开发者模式后，您可以在“开发者选项”（设置 -> 搜索“开发者选项”）中启用一些实用的开发者功能，例如“保持唤醒状态”。


## 使用指令生成APK

1. Run the following command in
``$PDK_ROOT/vendor/google_shared/packages/desktop/Factory/factory/`` to build the
app
```sh
./gradlew assembleDebug
```
3. Deploy apk with the following command
```sh
adb install -d -g -t app/build/outputs/apk/debug/app-debug.apk
```

## Debug 方式
显示特定应用程序的日志
```sh
$ adb logcat --pid=$(adb shell pidof <package_name>) -v color
```

例如Factory APP
```sh
$ adb logcat --pid=$(adb shell pidof com.google.android.factory.factory) -v
color
```
## Android SDK 环境配置
```sh
echo 'export ANDROID_HOME=/home/$USER/Android/Sdk' >> ~/.zshrc
echo 'export PATH=$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$PATH' >> ~/.zshrc

echo 'export ANDROID_HOME=/home/$USER/Android/Sdk' >> ~/.bashrc
echo 'export PATH=$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$PATH' >> ~/.bashrc

source ~/.zshrc    source ~/.bashrc

Ex:
export PDK_ROOT=/home/lyn/aluminiumos
export KEYSTORE_ROOT=/home/lyn/aluminiumos/ocicat/Test
export KEY_NAME=ocicat_key
export ANDROID_HOME=/home/lyn/Android/Sdk
export ANDROID_HOME=/path/to/your/android/sdk
export PATH=$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$PATH
```
## 生成APK签名密钥
By default, Soong signs platform applications with the key located in
``$PDK_ROOT/build/target/product/security/``. The following commands generate the
keystore for Gradle as $PATH_OF_OUTPUT_KEYSTORE_FILE:
```sh
$ openssl pkcs8 -inform DER -nocrypt -in $PDK_ROOT/build/target/product/security/platform.pk8 -out platform.key
$ openssl pkcs12 -export -in ~/aluminiumos/build/target/product/security/platform.x509.pem -inkey platform.key -out platform.p12 -name AndroidDebugKey -password pass:android
$ keytool -importkeystore \
-deststorepass android \
-destkeypass android \
-destkeystore $KEYSTORE_ROOT/$KEY_NAME \
-srckeystore platform.p12 \
-srcstoretype PKCS12 \
-srcstorepass android \
-alias AndroidDebugKey
```
**预期输出**:

``Importing keystore platform.p12 to /home/lyn/aluminiumos/test/ocicat_key...``


## 创建local.properties
touch ``$PDK_ROOT/vendor/google_shared/packages/desktop/Factory/factory/local.properties``

```sh
Example:
sdk.dir=/home/lyn/Android/Sdk
keystorePath=/home/lyn/aluminiumos/test/ocicat_key
```
keystorePath 跟随生成APK签名密钥的**OUTPUT_KEYSTORE_FILE** 


## Android Studio 

**参考官方网站** [Andriod Developer](https://developer.android.com/studio/install?hl=zh-cn)




