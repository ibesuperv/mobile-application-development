# Android Studio Setup, Project Creation, Architecture, Deployment & Logging

---

## 1. Introduction to Android Studio IDE

**Android Studio** is the official Integrated Development Environment (IDE) for Android application development, built on JetBrains' IntelliJ IDEA software and provided by Google.

### Key IDE Components & Tooling:
- **Gradle-based Build System**: Automates compilation using Android-specific Gradle plugins (`com.android.application`).
- **Android Virtual Device (AVD) Manager**: Configures virtual hardware emulators running compiled Android OS system images.
- **Dual-Mode Layout Editor**: Provides **Design View** (Visual layout grid + Palette pane of UI widgets) and **Text View** (Direct XML editing).
- **Android Monitor Pane**: Contains two primary runtime tabs:
  - **`logcat` tab**: Displays real-time system and application log messages.
  - **`Monitors` tab**: Tracks real-time app performance metrics (CPU, Memory, Network, GPU).
- **Android Debug Bridge (ADB)**: Command-line daemon facilitating host computer to device communication.

---

## 2. Emulators vs. Simulators (6-Mark Technical Comparison)

| Technical Dimension | Android Virtual Device (AVD Emulator) | iOS Simulator |
| :--- | :--- | :--- |
| **Core Definition** | Emulates both device hardware architecture and OS software environment. | Simulates target software API behavior on host system hardware without hardware emulation. |
| **Underlying Architecture** | Runs an actual compiled Android Linux kernel inside a QEMU hypervisor virtual machine. | Native desktop application executing binaries compiled specifically for host CPU architecture. |
| **Execution Accuracy** | Extremely high (accurately replicates ARM/x86 CPU instruction execution, memory limits, register states). | High for software flow, low for hardware constraints (e.g., thermal limits, low-level memory allocation limits). |
| **Resource Overhead** | Requires higher host RAM/CPU and hardware acceleration extensions (Intel HAXM, AMD-V, KVM). | Lightweight resource usage with fast launch times. |
| **Binary Execution** | Executes actual `.dex` bytecode and native C/C++ `.so` shared libraries. | Requires app code to be target-compiled for host processor (Simulator SDK target). |

---

## 3. Comprehensive Android Project Folder Structure (6-Mark Exam Answer)

When a project is created, Android Studio structures it under the **Project: Android View**. Below is the complete structural hierarchy and detailed explanation required for university examinations.

```
MyApplication/ (Project Root)
 ├── 1. manifests/
 │    └── AndroidManifest.xml
 ├── 2. java/
 │    ├── com.example.android.helloworld/
 │    │    └── MainActivity.java
 │    ├── com.example.android.helloworld (test)/
 │    └── com.example.android.helloworld (androidTest)/
 ├── 3. res/
 │    ├── layout/
 │    │    └── activity_main.xml
 │    ├── values/
 │    │    ├── strings.xml
 │    │    ├── colors.xml
 │    │    └── styles.xml / themes.xml
 │    ├── mipmap/
 │    │    ├── ic_launcher.png (mdpi, hdpi, xhdpi, xxhdpi, xxxhdpi)
 │    └── drawable/
 └── 4. Gradle Scripts/
      ├── build.gradle (Project: MyApplication)
      └── build.gradle (Module: app)
```

### Detailed Structural Breakdown:

1. **`manifests/` Folder (`AndroidManifest.xml`)**:
   - Located at the root of the app module directory.
   - Essential metadata manifest required by the Android runtime environment before launching any app component.
   - Declares the unique Java package name, global application configuration, all 4 core components (Activities, Services, Broadcast Receivers, Content Providers), system permissions, and themes.

2. **`java/` Folder**:
   - Contains Java source code files organized under module package names starting with reverse-domain syntax (e.g., `com.example.android.helloworld`).
   - **`MainActivity.java`**: Customary primary entry Activity screen loaded when the user launches the app. Extends `AppCompatActivity` for backward compatibility.
   - **`(test)` Subdirectory**: Houses local unit tests executed directly on host development computer's JVM.
   - **`(androidTest)` Subdirectory**: Houses instrumented UI tests (using Espresso / UIAutomator) executed on physical or virtual Android devices.

3. **`res/` (Resources) Folder**:
   - Stores external non-code resources, assets, and UI layout definitions.
   - **`res/layout/`**: Holds XML resource files (e.g., `activity_main.xml`) defining the visual layout of views.
   - **`res/values/`**: Contains XML resource tables:
     - `strings.xml`: Encapsulates text strings (`app_name`) for reusability and internationalization (locale translation).
     - `colors.xml`: Color definitions assigned to UI components.
     - `styles.xml` / `themes.xml`: Defines app-wide themes (`AppTheme`).
   - **`res/mipmap/`**: Stores app launcher icons (`ic_launcher.png`) placed across density-specific folders (`mdpi`, `hdpi`, `xhdpi`, `xxhdpi`, `xxxhdpi`) matching device screen pixel densities.
   - **`res/drawable/`**: Contains bitmap graphics (PNG, JPEG) and vector drawables used in layouts.

4. **`Gradle Scripts/` Folder**:
   - Houses build automation scripts executed using Groovy DSL.
   - Contains `build.gradle (Project)` for top-level repo-wide configurations and `build.gradle (Module: app)` for module build configurations and external dependencies.

---

## 4. Deep-Dive: `AndroidManifest.xml` Anatomy & Attributes

### Complete XML Structure

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.android.helloworld">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">

        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

    </application>
</manifest>
```

### Detailed Attribute Breakdown (Textbook Specification):

1. **`xmlns:android="http://schemas.android.com/apk/res/android"`**:
   - Mandatory XML namespace definition enabling standard `android:` attribute prefixes.

2. **`package="com.example.android.helloworld"`**:
   - Defines the unique package name of the app. Serves as the primary process identifier inside Android runtime. *Must remain static after publishing*.

3. **`<application>` Root Tag & Attributes**:
   - **`android:allowBackup="true"`**: Enables automatic cloud backup and restore of app data without additional code. Default is `true` for target SDK $\ge$ 23 (Android 6.0 Marshmallow).
   - **`android:icon="@mipmap/ic_launcher"`**: Points to the launcher icon resource stored in `res/mipmap/`.
   - **`android:label="@string/app_name"`**: Sets the user-facing application title pointing to a string key defined inside `res/values/strings.xml`.
   - **`android:theme="@style/AppTheme"`**: Applies UI styling and window decoration rules defined in `res/values/styles.xml`.

4. **`<activity>` & `<intent-filter>` Component Tags**:
   - Every activity class **must** be declared via an `<activity>` tag (e.g., `android:name=".MainActivity"`).
   - **`<action android:name="android.intent.action.MAIN" />`**: Designates this activity as the main entry point of the app.
   - **`<category android:name="android.intent.category.LAUNCHER" />`**: Places the app launcher icon on the device home screen to start this activity.

5. **`minSdkVersion` vs `targetSdkVersion` Behavioral Rules**:
   - Declared historically via `<uses-sdk android:minSdkVersion="15" android:targetSdkVersion="24" />` (now set via module `build.gradle`).
   - **`minSdkVersion`**: Lowest API level required to install and run the app.
   - **`targetSdkVersion`**: Highest API version for which the app has been optimized and tested. If a device runs an OS version higher than `targetSdkVersion`, Android applies legacy backward-compatibility behavior adjustments (e.g., in Android 4.4 API 19+, `AlarmManager` calls are inexact by default to save battery unless `targetSdkVersion < 19`).

---

## 5. Detailed Breakdown of `build.gradle` (Module: app)

```groovy
apply plugin: 'com.android.application'

android {
    compileSdkVersion 24
    buildToolsVersion "24.0.1"

    defaultConfig {
        applicationId "com.example.android.helloworld2"
        minSdkVersion 15
        targetSdkVersion 24
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:24.2.1'
    testCompile 'junit:junit:4.12'
}
```

### Element Analysis:
- **`apply plugin: 'com.android.application'`**: Applies Android-specific Gradle tasks for packaging APKs.
- **`compileSdkVersion 24`**: Specifies API version used to compile Java code.
- **`defaultConfig`**: Overrides manifest SDK settings (`minSdkVersion`, `targetSdkVersion`, `applicationId`).
- **`dependencies`**: Includes local JARs (`fileTree`), Android Support Libraries (`appcompat-v7` for backward-compatible UI features), and testing frameworks (`junit`, `espresso-core`).
- **Project Syncing (`Sync Now`)**: Required whenever `build.gradle` changes to import new libraries and verify configuration syntax against `compileSdkVersion`.

---

## 6. Deploying & Testing Applications

### A. Virtual Device (AVD) Deployment
- **AVD Manager**: Creates virtual hardware configurations.
- **Density Mapping**: Device density settings (e.g., `xxhdpi`) dictate which `mipmap-xxhdpi` and layout resource subfolders Android pulls assets from at runtime.
- *Best Practice*: Keep the emulator running across development sessions to eliminate repeated OS boot times.

### B. Physical Hardware Device Deployment & Troubleshooting
- **Prerequisites**: USB data cable, OEM USB Drivers (Windows/Linux).
- **Enable USB Debugging Workflow**:
  1. Open `Settings > About phone`.
  2. Tap **Build number** **7 times** continuously.
  3. Return to `Settings > Developer options`.
  4. Toggle **USB Debugging** to `ON`.
- **Troubleshooting Steps if Device is Not Detected / Unauthorized**:
  1. Unplug and reconnect USB cable.
  2. Restart Android Studio.
  3. Navigate to `Settings > Developer options` $\rightarrow$ Tap **Revoke USB Debugging authorizations** $\rightarrow$ Reconnect USB and accept host computer RSA key authorization prompt.
  4. Install/reinstall OEM USB drivers.

---

## 7. Logging Infrastructure & Log Levels (`android.util.Log`) - Exam Detailed Topic

Logging is an indispensable debugging technique used to inspect execution paths, runtime variable values, and uncaught exceptions. Log output appears under the **`logcat` tab** in the **Android Monitor** pane.

### Complete Log Levels Hierarchy (Exam 6-Mark Question)

Android provides 5 primary log priority levels accessible via static methods in the `android.util.Log` class:

| Log Level | Method Call | Filter Priority | Exam Description & Specific Use Case |
| :--- | :--- | :--- | :--- |
| **Verbose** | `Log.v(TAG, msg)` | **Lowest** | Captures all log messages. Used for detailed, fine-grained trace logs during initial algorithm development. |
| **Debug** | `Log.d(TAG, msg)` | **Medium-Low** | Used for general development debugging messages (e.g., verifying variable state, loop counts, API payload responses). Visible when filtering by *Debug* or *Verbose*. |
| **Info** | `Log.i(TAG, msg)` | **Medium** | Captures significant runtime events (e.g., network connected, screen orientation changed, user logged in). Visible when filtering by *Info*, *Debug*, or *Verbose*. |
| **Warning** | `Log.w(TAG, msg)` | **Medium-High** | Highlights unexpected runtime events or non-critical performance issues that do not crash the app (e.g., fallback default value used due to missing key). Visible under *Warning* or higher. |
| **Error** | `Log.e(TAG, msg)` | **Highest** | Reports critical failure events, caught exceptions, and unhandled errors that break functionality. Visible under *Error* or higher. |

### Java Logging Implementation & Code Example

#### Best Practice Rules:
1. Define a `private static final String LOG_TAG` constant initialized with `MainActivity.class.getSimpleName()`.
2. Use the constant as the first argument (`TAG`) to facilitate filtering in `logcat`.
3. Pass the log message string as the second argument.

```java
package com.example.android.helloworld;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;

public class MainActivity extends AppCompatActivity {

    // 1. Define Log Tag Constant using Class Simple Name
    private static final String LOG_TAG = MainActivity.class.getSimpleName();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 2. Logging at different priority levels
        Log.v(LOG_TAG, "Verbose: Entering onCreate() method.");
        Log.d(LOG_TAG, "Debug: Activity layout activity_main inflated successfully.");
        Log.i(LOG_TAG, "Info: Application started with package com.example.android.helloworld.");
        
        try {
            int result = performDivision(10, 0);
        } catch (ArithmeticException e) {
            Log.w(LOG_TAG, "Warning: Division by zero attempted. Returning fallback default.");
            Log.e(LOG_TAG, "Error: Exception captured during calculation: " + e.getMessage());
        }
    }

    private int performDivision(int a, int b) {
        return a / b;
    }
}
```

### Viewing and Filtering Logs in Logcat Pane:
1. Open **Android Monitor** pane (bottom toolbar of Android Studio) $\rightarrow$ Select **logcat** tab.
2. **Log Level Dropdown**: Filter messages by selecting `Verbose`, `Debug`, `Info`, `Warn`, or `Error`.
3. **Search / Tag Filter**: Enter the custom `LOG_TAG` string (`MainActivity`) into the search field to filter out noise from OS system logs.
