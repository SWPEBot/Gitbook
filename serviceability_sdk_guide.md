# Serviceability SDK Guidance

[TOC]

## Introduction

This document provides guidance for OEM app developers on how to use the
Serviceability SDK within the Aluminium project. This SDK provides specific
functionalities for serviceability purposes, beyond what is available in the
standard Android SDK.

## SDK Types in Aluminium

Developers working on Aluminium have access to different types of Android SDKs:

1.  Android Public SDK:

    *   This is the standard, publicly available Android SDK.
    *   It can be downloaded and managed via Android Studio.
    *   Contains the public APIs for general Android app development.
    *   Reference:
        [Android Developers][5]

2.  Android System SDK:

    *   This SDK grants access to Android System APIs (often referred to as
        private or hidden APIs).
    *   It is *not* part of the public SDK and is distributed as part of the
        Aluminium (AL) build.
    *   Intended *only* for partners who have a legitimate need to use specific
        System APIs.
    *   The System SDK access and usage guidelines are under development. Please
        reach out to your TAMs if you need any help.

3.  Serviceability SDK:

    *   This is a custom SDK developed specifically for Aluminium serviceability
        requirements. It provides a dedicated set of APIs for factory and
        service-related apps.

    *   The JAR file and SDK documents are located within the Aluminium source
        tree:

        *   JAR:
            [`vendor/google_shared/packages/desktop/Factory/serviceability_sdk`][6]
        *   Document:
            [`vendor/google_shared/packages/desktop/Factory/serviceability_sdk_doc`][1]


## Serviceability SDK

### APIs

These are the APIs provided by the Serviceability SDK.

**Public APIs**: These are APIs intended for general OEM use. They are stable
and documented for public use within the Aluminium ecosystem. OEM apps should
**only** use these public APIs by default.

**System APIs (@SystemApi)**: These are restricted APIs used for sensitive
system-level operations. They are marked with the `@SystemApi` annotation in the
source code.
> [!IMPORTANT]
> OEM apps are **NOT** allowed to use System APIs unless they have received
> explicit permission from Google. If your app requires a Serviceability System
> API, you must reach out to your Google technical contact to request access.

#### Available Public APIs

The following are the currently available public APIs in `ServiceabilityManager`.

> [!WARNING]
> The list below may be out-of-date as the SDK evolves. For the most recent and
> detailed API documentation, please refer to the generated Javadoc in:
> [vendor/google_shared/packages/desktop/Factory/serviceability_sdk_doc/][1]

##### Hardware Information

*   `fetchBatteryInfo()`
*   `fetchSmartBatteryInfo()`
*   `fetchThermalInfo()`
*   `fetchEcThermalInfos()`
*   `fetchPhysicalCpusInfo()`
*   `fetchBlockDevicesInfo()`
*   `readCpuName()`
*   `readSoundCardName()`

##### Diagnostics & Tests

*   `runCpuStressTest(...)`
*   `runCpuCacheTest(...)`
*   `runPrimeSearchTest(...)`
*   `runFloatingPointAccuracyTest(...)`
*   `runMemoryTest(...)`
*   `stopLongRunningTest(...)`
*   `isFingerprintAlive()`

##### Component Control

*   `fetchFansSpeed()`
*   `setFanSpeed(...)`
*   `setFansSpeedAuto()`
*   `setLedColor(...)`
*   `setLedColorAuto(...)`

##### Manufacturing & Identification

*   `readMlbSerialNumber()`
*   `readImei()`
*   `readMeid()`
*   `readMfgSkuId()`
*   `readRegion()`
*   `readOemKey2()`
*   `readKbInfo()`
*   `readDeviceIdFromScratch()`
*   `readDeviceIdFromInfo()`
*   `ecI2cTransfer(...)`
*   `apI2cTransfer(...)`

### Permissions

The Serviceability SDK uses different permission tiers to control access:

*   `vendor.google.desktop.permission.BIND_SERVICEABILITY_SERVICE`: Required
    for **Public APIs**. This is a privileged permission that must be granted
    to your app.

### Using Serviceability SDK

To use the Serviceability SDK in your app, follow these configuration steps:

#### 1. Gradle Configuration (`build.gradle.kts`)

Add the Serviceability SDK JAR as a dependency in your app's `build.gradle.kts`
file. You'll need to adjust the path to the JAR based on your project structure
relative to the JAR's location.

```kotlin
dependencies {
    // Example: Assuming you've copied the JAR to your project's 'libs' folder
    // Adjust the path
    // "libs/vendor.google.libraries.desktop.serviceability.stubs.jar" as needed.
    implementation(
        files("libs/vendor.google.libraries.desktop.serviceability.stubs.jar")
    )
}
```

#### 2. Manifest Configuration (`AndroidManifest.xml`)

Declare the use of the shared library and any necessary permissions in your
`AndroidManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myoemapp">

    <application ...>

        <!-- Declare that the app uses the Serviceability shared library -->
        <uses-library
            android:name="vendor.google.libraries.desktop.serviceability"
            android:required="true" />

        <activity android:name=".MainActivity"> ... </activity>
    </application>
</manifest>
```

*   The `<uses-library>` tag ensures that the system links your app against the
    Serviceability shared library.
*   The `android:required="true"` means the app will not install if this
    library is not present on the system image.

### Core Components

SDK Source Code Reference:

*   [ServiceabilityManager.java][2]
*   [DesktopBuild.java][3]
*   [MinVersion.java][4]

#### `ServiceabilityManager`

The `ServiceabilityManager` is the primary entry point for interacting with the
Serviceability SDK. It provides a high-level Java API for gathering hardware
info, running diagnostics, and controlling device components.

> [!NOTE]
> Most methods in `ServiceabilityManager` are synchronous but may involve IPC
> to underlying native services. Ensure you handle potential `RemoteException`
> (rethrown as `RuntimeException`) and `SecurityException` if permissions are
> missing.

### Version Control and Compatibility
The Serviceability SDK provides mechanisms to ensure compatibility across
different Aluminium build versions.

#### `DesktopBuild`

The `DesktopBuild` class provides information about the current desktop build
version at runtime. You can use it to conditionally execute code based on the
device's build version.

```java
if (DesktopBuild.BUILD_VERSION >= DesktopBuild.Version.CL2B) {
    // Execute code supported in CL2B and later
}
```

Example versions include:

*   `CL2B`: 26Q2 ARSP release
*   `CL3B`: 26Q3 ARSP release
*   `MAIN`: Main development branch

#### `@MinVersion`

The `@MinVersion` annotation is used within the SDK to denote the minimum
`DesktopBuild.Version` required for a specific class, method, or field. When
using SDK APIs, pay attention to these annotations in the documentation or
source to ensure they are available on your target build.

```java
@MinVersion(DesktopBuild.Version.CL2B)
public void someNewApi() { ... }
```

If your app needs to support multiple versions, always wrap calls to newer APIs
with a `DesktopBuild.BUILD_VERSION` check.

[1]: https://arsp.googlesource.com/platform/vendor/google_shared/packages/desktop/Factory/+/refs/heads/main/serviceability_sdk_doc/
[2]: https://arsp.googlesource.com/platform/vendor/google/desktop/+/refs/heads/main/serviceability/serviceability-sdk/src/vendor/google/libraries/desktop/serviceability/ServiceabilityManager.java
[3]: https://arsp.googlesource.com/platform/vendor/google/desktop/+/refs/heads/main/serviceability/serviceability-sdk/src/vendor/google/libraries/desktop/serviceability/DesktopBuild.java
[4]: https://arsp.googlesource.com/platform/vendor/google/desktop/+/refs/heads/main/serviceability/serviceability-sdk/src/vendor/google/libraries/desktop/serviceability/MinVersion.java
[5]: https://developer.android.com/studio/releases/platforms
[6]: https://arsp.googlesource.com/platform/vendor/google_shared/packages/desktop/Factory/+/refs/heads/main/serviceability_sdk/
