# Serviceability Mock SDK Usage Guide

[TOC]

## Introduction

This document provides guidance for OEM app developers on how to use the
Serviceability Mock SDK within the Aluminium project. The Mock SDK allows
application developers to test and integrate with the Serviceability SDK without
the current dependency on a physical Aluminium device. This is particularly
useful for partners in early development stages (such as EVT or DVT) who need to
develop applications but lack physical hardware.

The mock SDK is designed to simulate successful initialization and execution of
all APIs, allowing the application to proceed without errors.

## Getting the Mocked SDK

You can get the mocked SDK by compiling the source or directly downloading from
Android CI.

### Compiling From Source

The source code for the mock library is located in the Aluminium source tree via
Gitiles: [Serviceability Mock SDK Source Code](https://arsp.googlesource.com/platform/vendor/google/desktop/+/refs/heads/main/serviceability/serviceability-sdk/mock/src/vendor/google/libraries/desktop/serviceability)

To compile the mock JAR locally:

1. Initialize your Aluminium build environment.

2. Build with `m dist`.

3. Once the build completes, retrieve the compiled JAR file at `out/disk/serviceability-sdk`

### Downloading from Android CI

If you do not have a local build environment, you can download the pre-compiled
JAR directly from Android Continuous Integration (CI):

1. **Access the Build Grid:** Navigate to the [ARSP Main Build Grid on ci.android.com](https://ci.android.com/builds/branches/arsp-main/grid?legacy=1).

2. **Locate Your Target:** On the left side of the screen, scroll down to find
the specific build target that matches your development environment.

3. **Select a Successful Build:** Look across the row for your selected target
and click on the most recent green square, which indicates the latest successful
build.

4. **Navigate to Artifacts:** In the build details menu that appears, click on
the Artifacts tab to view the generated files for that build.

5. **Download the JAR:** Search the list of build outputs for the mock SDK JAR
artifact and download it. The JAR file is in the `desktop-serviceability-sdk`
folder. This mock JAR file is designed to use the exact same package name as the
standard production SDK.

## Deploying and Building with the Mock SDK

To ensure a seamless developer experience, the mock JAR file is built using the
exact same package name as the production SDK. This allows developers to switch
between the real SDK and the mock SDK simply by replacing the JAR file in their
environment, requiring zero changes to the application's source code.

To use the Mock SDK in your app, follow these configuration steps:

### 1. Gradle Configuration (`build.gradle.kts`)

Replace your existing production Serviceability SDK dependency with the Mock SDK
JAR. Add the Mock SDK JAR as a dependency in your app's build.gradle.kts file.
You will need to adjust the path to the JAR based on your project structure
relative to the JAR's location:

```kotlin
dependencies {
    // Example: Assuming you've copied the JAR to your project's 'libs' folder
    // Adjust the path
    // "libs/vendor.google.libraries.desktop.serviceability-mock.stubs.jar" as needed.
    implementation(
        files("libs/vendor.google.libraries.desktop.serviceability-mock.stubs.jar")
    )
}
```

> [!NOTE]
> Because the mock SDK returns hardcoded values natively, you can compile it
> using implementation for local VM/Emulator testing, whereas the production SDK
> typically requires compileOnly alongside the system image.

### 2. Manifest Configuration (`AndroidManifest.xml`)

Your application's source code and manifest do not need any modifications to
accommodate the mock SDK. Continue to declare your permissions and shared
library tags as you normally would for the production SDK.

## Hardcoded Behavior

All function calls in the Mock SDK will return non-error, default values:

* **Primitive numeric types** (e.g., `int`, `long`): will return `0`.

* **Boolean values**: will return `false`.

* **Collections** (e.g., `List`, `Map`): will return empty collections.

* **Strings**: will return the name of the string.

* **@NonNull Objects**: will return a constructed object with all internal fields initialized to empty/default values.

* **@Nullable Objects**: will return `null`.

## Limitations

> [!WARNING]
> **Non-Configurable Output**: The return values are hardcoded inside the
> provided JAR file. OEMs and app developers cannot change or determine the
> specific return data.

The mock is strictly intended for basic integration and compilation verification
(i.e., verifying the app can call the APIs without crashing). It is not suitable
for testing complex application logic that depends on specific data states
(e.g., simulating low battery, various sensor readings, or device disconnection
events).