# Development Guide for Serviceability Apps

[TOC]

## Background

After completing the Serviceability Customization Request, the Play-signed stub
APK will be pre-installed on the system image of the requested devices. This
stub APK secures the necessary privileged permissions for the serviceability
app.

On a release image, the serviceability app can only be updated from the Play
Store. The Android system prevents updating a pre-installed app with an app
signed by a different certificate. This means you cannot directly install a
dev-signed APK over the pre-installed Play-signed stub APK, which poses
challenges for local development and testing.

This guide provides instructions on how to replace the pre-installed Play-signed
stub APK with your development-signed version on a `userdebug` build for testing
purposes.

## Local Development

### Prerequisites

* A development key/certificate used to sign your local builds.
* Your stub app APK signed with your development key (e.g., `YourServiceabilityAppStub-dev.apk`).
* The package name of your serviceability app (e.g., `com.example.serviceapp`).
* A device running a userdebug image. This device must already have your Play-signed stub APK and related configurations pre-installed.

### Device Setup: Replacing the Stub APK

To replace the Play-signed stub APK with your dev-signed version, follow these steps:

1.  **Connect to the Device and Gain Root Access:**

    Ensure your device is connected and recognized by `adb`.

    ```shell
    adb root
    ```

2.  **Disable Verity and Remount the System Partition:**

    This step makes the system partition writable. This process requires a
    reboot.

    ```shell
    adb disable-verity
    adb reboot
    ```
    Wait for the device to fully restart.

3.  **Re-establish Root and Remount:**

    After the reboot, regain root access and remount the partitions:

    ```shell
    adb root
    adb remount
    ```
    You should see output like `Remount succeeded`. If you see errors, ensure
    verity is disabled and the device is on a `userdebug` build.

4.  **Identify the Stub APK Path:**

    Find the exact path of the pre-installed stub APK on the device using its
    package name. Replace `com.example.serviceapp` with your app's actual
    package name.

    ```shell
    # Replace com.example.serviceapp with your package name
    PKG_ID=com.example.serviceapp
    APK_PATH=$(adb shell pm list packages -f "$PKG_ID" | sed -n "s/package:\(.*\)=${PKG_ID}/\1/p")
    echo "APK Path on device: $APK_PATH"
    ```
    The output will be something like `APK Path on device: /product/priv-app/YourServiceabilityAppStub/YourServiceabilityAppStub.apk`.

5.  **Replace the APK:**

    Push your dev-signed stub APK to the path obtained in the previous step.
    Make sure the local path to your dev-signed APK is correct.

    ```shell
    # Replace path/to/YourServiceabilityAppStub-dev.apk with the actual path to your dev APK.
    LOCAL_APK_PATH=path/to/YourServiceabilityAppStub-dev.apk

    adb push $LOCAL_APK_PATH $APK_PATH
    ```
    *Important:* The destination path and APK name on the device (`$APK_PATH`)
    *must* match the original pre-installed APK path and name.

    > [!WARNING]
    > **Additional Privileged Permissions:** If your development APK requires
    > additional privileged permissions that were not in the original stub, you
    > must also update the privileged permission allowlist file on the device
    > (typically `/product/etc/permissions/privapp-permissions-com.example.serviceapp.xml`).
    >
    > If you do not update the allowlist to match the new permissions, the device
    > may fail to boot (boot loop) due to permission mismatch.
    >
    > ```shell
    > # Push the updated permission XML if needed
    > adb push path/to/privapp-permissions-com.example.serviceapp.xml /product/etc/permissions/privapp-permissions-com.example.serviceapp.xml
    > ```

6.  **Reboot the Device:**

    Apply the changes by rebooting the device:

    ```shell
    adb reboot
    ```

7.  **Verify the Replacement:**

    After the device restarts, you can verify that your dev-signed stub APK is
    now pre-installed. You can check the version code or name:

    ```shell
    adb shell dumpsys package $PKG_ID | grep -E "versionCode|versionName"
    ```
    This should show the version information from your dev-signed stub APK.

### Iterative Development Workflow

Once the dev-signed stub APK has replaced the Play-signed stub APK in the system
image, you can install subsequent updates signed with the *same development key*
using the standard `adb install` command:

```shell
adb install -r path/to/YourServiceabilityApp-dev.apk
```

This allows for a typical development cycle of build, install, and test without
needing to repeat the system partition modification steps.

### Restoring the Original Play-Signed Stub APK

To restore the original Play-signed stub APK, please flash the device back to an
official build image using the standard flashing tools.
