# Development Guide for PWA Extension Wrapper

[TOC]

## Introduction

### Stub

The example of Stub APK is under `pwa_ext_wrapper/stub`.

After configuring the `signingConfigs` under `stub/app/build.gradle.kts`. The
signed APK and signed App Bundle can be generated with
`./gradlew assembleRelease` and `./gradlew bundleRelease`.

`app/build.gradle.kts`:
```
android {
  ...

  // Update this part.
  signingConfigs {
    create("release") {
      storeFile = file("/PATH/TO/test_key/upload-keystore.jks")
      storePassword = "PASSWORD"
      keyAlias = "upload"
      keyPassword = "PASSWORD"
    }
  }

  buildTypes {
    release {
      ...

      signingConfig = signingConfigs.getByName("release")
    }
  }
  ...
}
```

Build APK.
```bash
$ ./gradlew assembleRelease

# Check the generated APK.
$ ls app/build/outputs/apk/release/
```

Build App Bundle.
```bash
$ ./gradlew bundleRelease

# Check the generated App Bundle.
$ ls app/build/outputs/bundle/release/
```

### Wrapper

The example of wrapper app is under `pwa_ext_wrapper/wrapper`.

We need to update the below files to configure your PWA and extension.

* `wrapper/app/src/main/java/com/googlechromelab/diag/Config.kt`
  * Update `appUrl` with your PWA's URL.
* `wrapper/app/src/main/res/raw/service_worker.js`
  * Replace the content of this file with your extension's JS code.

Then, the APK and App Bundle can be generated via `./gradlew assembleRelease`
and `./gradlew bundleRelease` after updating the `signingConfigs` under
`wrapper/app/build.gradle.kts`.

## Supported APIs

APIs in Telemetry Extension API are proxied to Android APIs in the `wrapper`
app.

Here is a summary of API support status.

### Telemetry

Supported:

* chrome.os.telemetry.getAudioInfo
* chrome.os.telemetry.getBatteryInfo
* chrome.os.telemetry.getCpuInfo
* chrome.os.telemetry.getInternetConnectivityInfo
* chrome.os.telemetry.getMarketingInfo
* chrome.os.telemetry.getMemoryInfo
* chrome.os.telemetry.getUsbBusInfo
* chrome.os.telemetry.getVpdInfo
* chrome.os.telemetry.getStatefulPartitionInfo
* chrome.os.telemetry.getDisplayInfo
* chrome.os.telemetry.getThermalInfo
* chrome.os.telemetry.getOemData
* chrome.os.telemetry.getNonRemovableBlockDevicesInfo

Won't support:

* chrome.os.telemetry.getOsVersionInfo
  * This version format is not supported. Use `Build.DISPLAY` instead.
* chrome.os.telemetry.getTpmInfo
  * TPM is not available in Android.

### Diagnostics

Supported:

* chrome.os.diagnostics.runAcPowerRoutine
* chrome.os.diagnostics.runAudioDriverRoutine
* chrome.os.diagnostics.runBatteryCapacityRoutine
* chrome.os.diagnostics.runBatteryChargeRoutine
* chrome.os.diagnostics.runBatteryDischargeRoutine
* chrome.os.diagnostics.runBatteryHealthRoutine
* chrome.os.diagnostics.runBluetoothDiscoveryRoutine
* chrome.os.diagnostics.runBluetoothPowerRoutine
* chrome.os.diagnostics.runBluetoothScanningRoutine
* chrome.os.diagnostics.runCpuCacheRoutine
* chrome.os.diagnostics.runCpuFloatingPointAccuracyRoutine
* chrome.os.diagnostics.runCpuPrimeSearchRoutine
* chrome.os.diagnostics.runCpuStressRoutine
* chrome.os.diagnostics.runDnsResolutionRoutine
* chrome.os.diagnostics.runDnsResolverPresentRoutine
* chrome.os.diagnostics.runFanRoutine
* chrome.os.diagnostics.runFingerprintAliveRoutine
* chrome.os.diagnostics.runGatewayCanBePingedRoutine
* chrome.os.diagnostics.runLanConnectivityRoutine
* chrome.os.diagnostics.runMemoryRoutine
* chrome.os.diagnostics.runSensitiveSensorRoutine
* chrome.os.diagnostics.runSignalStrengthRoutine

Under development (Expected to be supported after 2026 Q2 release):

* chrome.os.diagnostics.runBluetoothPairingRoutine
* chrome.os.diagnostics.runDiskReadRoutine
* chrome.os.diagnostics.runEmmcLifetimeRoutine
* chrome.os.diagnostics.runNvmeSelfTestRoutine
* chrome.os.diagnostics.runSmartctlCheckRoutine
* chrome.os.diagnostics.runUfsLifetimeRoutine

Won't support:

* chrome.os.diagnostics.runPowerButtonRoutine
  * Power button events are not available in Android.

### Diagnostics V2

* chrome.os.diagnostics.isRoutineArgumentSupported
* chrome.os.diagnostics.createRoutine
* chrome.os.diagnostics.cancelRoutine
* chrome.os.diagnostics.isRoutineArgumentSupported
* chrome.os.diagnostics.replyToRoutineInquiry
* chrome.os.diagnostics.startRoutine

Supported argument (`CreateRoutineArgumentsUnion`):

* memory
* fan
* ledLitUp

Under development (Expected to be supported after 2026 Q2 release):

* volumeButton
* cameraFrameAnalysis
* keyboardBacklight

Won't support:

* networkBandwidth
  * Use https://github.com/m-lab/ndt7-client-android

### Events

Supported:

* chrome.os.events.onAudioJackEvent
* chrome.os.events.onExternalDisplayEvent
* chrome.os.events.onTouchscreenConnectedEvent
* chrome.os.events.onTouchscreenTouchEvent
* chrome.os.events.onStylusConnectedEvent
* chrome.os.events.onStylusTouchEvent
* chrome.os.events.onUsbEvent

Under development (Expected to be supported after 2026 Q2 release):

* chrome.os.events.onLidEvent
* chrome.os.events.onPowerEvent
* chrome.os.events.onSdCardEvent
* chrome.os.events.onStylusGarageEvent
* chrome.os.events.onTouchpadButtonEvent
* chrome.os.events.onTouchpadConnectedEvent
* chrome.os.events.onTouchpadTouchEvent

Won't support:

* chrome.os.events.onKeyboardDiagnosticEvent
  * Use `android.view.KeyEvent` to observe key event directly
