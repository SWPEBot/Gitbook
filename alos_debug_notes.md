## Connect to DUT whit ADB

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
Enter interactive shell
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
$PDK_ROOT/vendor/google_shared/packages/desktop/Factory/factory/ to build the
app
```sh
./gradlew assembleDebug
```
3. Deploy apk with the following command
```sh
adb install -d -g -t app/build/outputs/apk/debug/app-debug.apk
```

## Debugging tips
Show logs for a specific app
```sh
$ adb logcat --pid=$(adb shell pidof <package_name>) -v color
```
Shell
Example for factory app
```sh
$ adb logcat --pid=$(adb shell pidof com.google.android.factory.factory) -v
color
```

## Generate signing key
By default, Soong signs platform applications with the key located in
$PDK_ROOT/build/target/product/security/. The following commands generate the
keystore for Gradle as $PATH_OF_OUTPUT_KEYSTORE_FILE:
```sh
$ openssl pkcs8 -inform DER -nocrypt -in
$PDK_ROOT/build/target/product/security/platform.pk8 -out platform.key
$ openssl pkcs12 -export -in
$PDK_ROOT/build/target/product/security/platform.x509.pem -inkey platform.key
-out platform.p12 -name AndroidDebugKey -password pass:android
$ keytool -importkeystore \
-deststorepass android \
-destkeypass android \
-destkeystore $PATH_OF_OUTPUT_KEYSTORE_FILE \
-srckeystore platform.p12 \
-srcstoretype PKCS12 \
-srcstorepass android \
-alias AndroidDebugKey
```

## Setup properties for build script
Add $PDK_ROOT/vendor/google_shared/packages/desktop/Factory/factory/local.properties
with the following lines under
```sh
sdk.dir={**absoulute path** to your sdk directory} // usually ~/Android/Sdk,
this should match the path you entered when you install SDK in step 2
keystorePath={**absoulute path** to your key} // this should the same key that
signs the rest of the Android platform
```
The sdk.dir is the absolute path to the directory of installed sdk. This should match the path you
entered in step 2.
The keystorePath is the absolute path of the generated keystore
file($PATH_OF_OUTPUT_KEYSTORE_FILE) which is created in step 4.
Example:
```sh
sdk.dir=/usr/local/google/home/myusername/Android/Sdk
keystorePath=/usr/local/google/home/myusername/pdk/vendor/google_shared/package
s/desktop/Factory/factory/platform.keystore
```
