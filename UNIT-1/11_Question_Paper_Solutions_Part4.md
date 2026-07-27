# Question Paper Solutions (Part 4): APK, ADB, OS Architecture & Manifest

---

## Question 18: What is .APK? List and Explain Its Components (10 Marks)

### A. What is an .APK File? (3 Marks)
An **APK (Android Package)** file is the final compiled archive format (`.apk`) used by the Android OS to distribute and install mobile applications. It is essentially a specialized **ZIP archive** that packages compiled Java/Kotlin bytecode, application resources, assets, manifest definitions, and cryptographic signature certificates.

```
                           APK ARCHITECTURE STRUCTURE
                                        |
     +----------------------------------+----------------------------------+
     |                                  |                                  |
     v                                  v                                  v
classes.dex                         AndroidManifest.xml                res/ & resources.arsc
(Compiled Dalvik Bytecode)          (Binary XML Manifest)              (Compiled UI layout & resources)
     |                                  |                                  |
     v                                  v                                  v
assets/                             META-INF/                          lib/
(Uncompiled Raw Assets)             (CERT.SF, CERT.RSA Signatures)     (Native C/C++ .so Libraries)
```

---

### B. List & Explanation of APK Components (7 Marks)

1. **`classes.dex` (Dalvik Executable)**:
   - Contains all application Java/Kotlin source code compiled into **Dalvik Bytecode**.
   - If an app is large, multidex generates multiple DEX files (`classes2.dex`, `classes3.dex`).
2. **`AndroidManifest.xml` (Binary XML)**:
   - The compiled binary version of the application manifest, containing permissions, hardware requirements, and component declarations.
3. **`res/` (Compiled Resources Directory)**:
   - Contains compiled application resources including XML layouts, drawables, and raw assets not compiled into `resources.arsc`.
4. **`resources.arsc` (Compiled Resource Index)**:
   - Contains pre-compiled binary tables mapping resource IDs (`R.id`, `R.string`) to their actual string, color, or dimension values.
5. **`META-INF/` (Digital Signature Directory)**:
   - Holds security verification files:
     - `MANIFEST.MF`: SHA-1 hashes of all files.
     - `CERT.SF`: Hashes of manifest entries.
     - `CERT.RSA`: The public key certificate used to verify app integrity.
6. **`assets/` (Uncompiled Raw Assets)**:
   - Stores raw, uncompiled assets accessed via `AssetManager` (e.g., custom fonts, SQLite `.db` seed files, WebGL assets).
7. **`lib/` (Native Libraries)**:
   - Contains compiled C/C++ architecture-specific `.so` native library binaries (`armeabi-v7a`, `arm64-v8a`, `x86_64`).

---

## Question 19: Write a Short Note On (10 Marks)

### A. ADB – Android Debug Bridge (5 Marks)
**Android Debug Bridge (ADB)** is a versatile command-line tool that enables communication between a development computer and a connected Android device or emulator.

```
                          ADB TRIO ARCHITECTURE
                                     |
   DEVELOPMENT PC                                      ANDROID DEVICE
+------------------+         TCP / USB Socket        +------------------+
|   ADB Client     | <-----------------------------> |    ADB Daemon    |
|   (Command Line) |                                 |     (adbd)       |
+--------+---------+                                 +------------------+
         |
         v
+------------------+
|    ADB Server    |
| (Background PC)  |
+------------------+
```

#### Components of ADB:
1. **Client**: Runs on the development machine (triggered via `adb` terminal commands).
2. **Daemon (`adbd`)**: Runs as a background process on the Android device/emulator.
3. **Server**: Background process on host PC that manages communication between client and daemon.

#### Key Functions & Command Examples:
- `adb devices`: Lists all connected physical devices and running emulators.
- `adb install app.apk`: Installs an APK file onto the device.
- `adb logcat`: Streams real-time system debug logs to terminal.
- `adb shell`: Opens an interactive Linux shell terminal on the target device.

---

### B. Android Manifest (`AndroidManifest.xml`) (5 Marks)

The **`AndroidManifest.xml`** file is a mandatory configuration file located at the root of every Android project. It presents essential app metadata to the Android OS before any app code can execute.

#### 5 Key Functions of `AndroidManifest.xml`:
1. **Component Registration**: Declares all 4 core components (`<activity>`, `<service>`, `<receiver>`, `<provider>`).
2. **Permission Declaration**: Lists required system permissions via `<uses-permission>` (e.g. `INTERNET`, `CAMERA`).
3. **Hardware & Feature Requirements**: Specifies minimum required hardware via `<uses-feature>` (e.g., GPS, NFC).
4. **App Naming & Branding**: Defines app icon (`android:icon`), title string (`android:label`), and theme (`android:theme`).
5. **Entry Point Identification**: Registers the launcher Activity via `<intent-filter>` containing `ACTION_MAIN` and `CATEGORY_LAUNCHER`.

---

## Question 20: Explain the Layered Architecture of Android OS (10 Marks)

Android OS is built on a **5-Layer Software Stack** operating on top of the Linux Kernel:

```
                         ANDROID OS 5-LAYER STACK
+-----------------------------------------------------------------------+
| Layer 5: APPLICATIONS LAYER (System & User Installed Apps)            |
+-----------------------------------------------------------------------+
| Layer 4: APPLICATION FRAMEWORK LAYER (ActivityManager, ViewSystem)   |
+-----------------------------------------------------------------------+
| Layer 3: NATIVE LIBRARIES LAYER    |   ANDROID RUNTIME (ART)          |
|  (SQLite, WebKit, OpenGL, Media)   |   (Core Libraries + AOT/JIT)     |
+-----------------------------------------------------------------------+
| Layer 1: LINUX KERNEL LAYER (Display, Camera, Wi-Fi, Memory Drivers)  |
+-----------------------------------------------------------------------+
```

### Detailed Layer Breakdown:

1. **Linux Kernel Layer (Layer 1 - Foundation)**:
   - Acts as an abstraction layer between device hardware and the software stack.
   - Handles low-level drivers (Display, Camera, Flash Memory, Wi-Fi), power management, and process security.
2. **Native Libraries Layer (Layer 3 - C/C++)**:
   - Contains high-performance compiled C/C++ libraries: `SQLite` (Database), `WebKit` (Browser engine), `OpenGL ES` (3D graphics), `Surface Manager` (Screen composition), `Media Framework` (Audio/Video).
3. **Android Runtime (ART) Layer (Layer 3 - Parallel)**:
   - Includes Core Java Libraries and the **Android Runtime (ART)** execution engine (compiles DEX bytecode into native machine instructions via AOT/JIT).
4. **Application Framework Layer (Layer 4 - Java APIs)**:
   - Provides high-level Java classes and APIs for app developers: `ActivityManager`, `ContentProvider`, `NotificationManager`, `View System`.
5. **Applications Layer (Layer 5 - Top)**:
   - The user-facing layer containing both pre-installed system apps (Phone, Contacts, Browser) and third-party apps installed from the Play Store.

---

## Question 1 (2020-2021): Compare Android with iOS & Explain AVD and SDK Manager (10 Marks)

### A. Comparison of Android OS with iOS & Key Features (5 Marks)

| Feature Dimension | Android OS (Google) | iOS (Apple) |
| :--- | :--- | :--- |
| **Openness** | **Open Source** (Apache/AOSP license; open customization). | Closed Source / Proprietary (Restricted to Apple hardware). |
| **Hardware Hardware Vendors** | Multi-vendor ecosystem (Samsung, Xiaomi, OnePlus, Google). | Exclusively manufactured by Apple Inc. |
| **App Distribution** | Multiple stores (Google Play, Amazon Appstore, APK sideloading). | Exclusively distributed via Apple App Store. |
| **Programming Language** | Java, Kotlin. | Swift, Objective-C. |

#### Key Features of Android OS:
- **Multitasking & Background Execution**: Concurrent process execution using Services and WorkManager.
- **Customizability**: Rich home-screen widgets, launcher customizers, and themes.
- **Unified Data Sharing**: Secure inter-app data sharing via Content Providers.

---

### B. Briefly Explain AVD and SDK Manager (5 Marks)

1. **AVD (Android Virtual Device)**:
   - An emulator configuration specifying hardware profile (RAM, screen size) and OS system image (API level) to test Android apps on a PC without physical hardware.
2. **SDK Manager**:
   - A tool integrated into Android Studio used to download, update, and manage Android SDK Platform versions, build-tools, emulator images, and Google APIs.

---

## Question 2 (2020-2021): Need & Structure of AndroidManifest.xml & /res Directory (10 Marks)

### A. Justify Need & Basic Structure of `AndroidManifest.xml` (5 Marks)

#### Why `AndroidManifest.xml` is Necessary:
The Android OS MUST know the structure, permissions, and main entry activity of an application BEFORE executing any code. Without the Manifest, the OS cannot bind intents, verify permissions, or launch activities.

#### Basic XML Structure:
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.sampleapp">

    <!-- System Permission Declarations -->
    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:icon="@mipmap/ic_launcher"
        android:label="Sample App"
        android:theme="@style/Theme.AppCompat">

        <!-- Activity Registration -->
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

    </application>
</manifest>
```

---

### B. Elaborate on Resource File (`/res` directory) in Android System (5 Marks)

The **`/res`** directory contains non-code visual, textual, and layout assets separated from Java code:

1. **`res/layout/`**: Contains XML UI layout definitions (`activity_main.xml`).
2. **`res/drawable/`**: Contains images (PNG, JPEG) and Vector Drawables (`ic_launcher.xml`).
3. **`res/values/`**: Contains primitive value XML files:
   - `strings.xml`: Translatable UI text strings.
   - `colors.xml`: App color palette definitions.
   - `dimens.xml`: Dimension sizes (`dp`, `sp`).
   - `styles.xml` / `themes.xml`: Global app styling rules.
4. **`res/mipmap/`**: Holds app launcher icons across density buckets (`hdpi`, `xxhdpi`).
5. **`res/menu/`**: XML definitions for options and context menus.
6. **`res/raw/`**: Uncompiled raw media files (MP3 audio, MP4 video).
