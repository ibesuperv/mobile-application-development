# Question Paper Solutions: Unit-1 Core Topics

---

## 1. What is Android Runtime? Explain Virtual Device. (10 Marks)

### A. Android Runtime (ART & DVM Overview) - 5 Marks

**Android Runtime** is the execution environment used by the Android operating system to execute application bytecode. It translates compiled Java/Kotlin bytecode into machine-level native instructions that the device CPU can execute.

```
                      BYTECODE EXECUTION EVOLUTION
                                   |
       +---------------------------+---------------------------+
       |                                                       |
       v                                                       v
DALVIK VIRTUAL MACHINE (DVM)                           ANDROID RUNTIME (ART)
- Legacy (Android 1.0 to 4.4)                          - Modern (Android 5.0+ / API 21+)
- Just-In-Time (JIT) Compilation                       - Ahead-Of-Time (AOT) + Hybrid JIT Compilation
- Converts .dex to native code at runtime              - Pre-compiles .dex to native ELF (.oat) at install
- Higher battery drain & CPU overhead                  - Faster execution, lower latency & memory footprint
```

#### 1. Dalvik Virtual Machine (DVM) vs. Android Runtime (ART):

| Feature Dimension | Dalvik Virtual Machine (DVM) | Android Runtime (ART) |
| :--- | :--- | :--- |
| **Compilation Method** | **Just-In-Time (JIT)** compilation (compiles code on-the-fly while app runs). | **Ahead-Of-Time (AOT)** compilation + hybrid JIT (compiles code during app installation). |
| **Execution Speed** | Slower startup and execution due to continuous runtime compilation. | **Significantly faster app launch** and smooth UI execution. |
| **Garbage Collection (GC)** | Frequent GC pauses, causing screen stutter ("jank"). | Optimized GC with reduced pause times and better memory compaction. |
| **Battery & CPU Impact** | Higher CPU consumption every time app executes code. | Lower CPU usage and **extended battery life**. |

---

### B. Virtual Device (Android Virtual Device - AVD) - 5 Marks

An **Android Virtual Device (AVD)** is an emulator configuration that simulates a physical Android device on your development computer (laptop/desktop).

```
                      AVD ARCHITECTURE COMPONENTS
                                   |
    +------------------------------+------------------------------+
    |                              |                              |
    v                              v                              v
HARDWARE PROFILE             SYSTEM IMAGE                   EMULATOR ENGINE
(Screen size, RAM, sensors,  (Target Android OS API level,   (QEMU virtualization running
 camera, buttons)             x86 / ARM architecture)        on host OS)
```

#### Key Components & Features of AVD:
1. **Hardware Profile**: Defines physical characteristics such as screen size, resolution, RAM allocation, camera availability, and hardware keys.
2. **System Image**: Specifies the target Android OS version (e.g. Android 13 / API 33) and processor architecture (`x86_64` for high-speed host emulation).
3. **Emulator Engine (QEMU / HAXM)**: Uses Intel HAXM or Hyper-V hardware virtualization on the host machine to execute Android OS code at near-native speed.
4. **Purpose**: Enables developers to test application behavior, screen responsiveness, rotation, and different API levels without purchasing physical test devices.

---

## 2. Android Architecture Layers & SQLite Database (10 Marks Each)

The Android Operating System is structured as a 5-layer software stack. Below is the full architecture breakdown covering **Kernel**, **Libraries**, and **Application Framework** layers and their relation to the **SQLite Database**.

```
                         ANDROID SOFTWARE STACK
+-----------------------------------------------------------------------+
|                         APPLICATION LAYER                             |
|       (System Apps, User Apps: Launcher, Contacts, Browser)          |
+-----------------------------------------------------------------------+
|                    APPLICATION FRAMEWORK LAYER                        |
|   (ActivityManager, ContentProvider, LocationManager, ViewSystem)    |
+-----------------------------------------------------------------------+
|       LIBRARIES LAYER        |          ANDROID RUNTIME (ART)         |
|  (SQLite, WebKit, OpenGL)    |     (Core Libraries + DVM/ART)         |
+-----------------------------------------------------------------------+
|                       LINUX KERNEL LAYER                              |
|  (Display Driver, Camera Driver, Flash Memory Driver, Power Mgmt)     |
+-----------------------------------------------------------------------+
```

---

### Question 2: What is the requirement of the Kernel Layer in Android Architecture and SQLite Database? (10 Marks)

#### A. Role & Requirement of the Linux Kernel Layer (6 Marks):
The **Linux Kernel** sits at the absolute foundation of the Android software stack (Layer 1). It provides an abstraction between physical device hardware and the rest of the software stack.
- **Hardware Drivers**: Contains essential device drivers including Display, Camera, Wi-Fi, Bluetooth, Audio, and Flash Memory drivers.
- **Power Management (Binder IPC)**: Manages battery consumption through low-memory killers and CPU sleep states.
- **Process & Memory Management**: Manages process isolation, RAM allocation, and thread execution security.

#### B. Relation of Kernel Layer to SQLite Database (4 Marks):
- **Disk File I/O Abstraction**: SQLite stores database data inside local files (`/data/data/<package_name>/databases/*.db`). The Linux Kernel layer provides the underlying **Flash Memory Drivers** and **EXT4/F2FS File System I/O operations**.
- **Process Security & Permissions**: Kernel-level user IDs (UIDs) ensure that an app's SQLite `.db` file is strictly isolated and readable/writable **only by the owning app process**.

---

### Question 3: What is the requirement of the Library Layer in Android Architecture and SQLite Database? (10 Marks)

#### A. Role & Requirement of the Native C/C++ Libraries Layer (6 Marks):
Sitting above the Kernel is the **C/C++ Native Libraries Layer** (Layer 2). These are high-performance compiled native libraries that handle system-critical tasks:
- **`SQLite`**: Provides embedded relational database support for structured data storage.
- **`WebKit` / `Chromium`**: Powering browser engine and `WebView` components.
- **`OpenGL ES` / `SGL`**: 2D and 3D graphics rendering engines.
- **`Surface Manager`**: Composes UI elements on screen across window layers.
- **`Media Framework`**: Playback and recording of audio/video formats (MPEG4, MP3, AAC).

#### B. Relation of Library Layer to SQLite Database (4 Marks):
- **Native SQLite Engine Execution**: The **C-based SQLite engine** lives directly inside this native Library Layer.
- When Java code calls `SQLiteDatabase.insert()` or `query()`, the call passes down via JNI (Java Native Interface) to execute raw SQL operations inside the high-speed C-engine of the SQLite library.

---

### Question 4: What is the requirement of the Framework Layer in Android Architecture and SQLite Database? (10 Marks)

#### A. Role & Requirement of the Application Framework Layer (6 Marks):
The **Application Framework Layer** (Layer 4) provides Java abstractions and APIs that developers use directly to build Android applications:
- **`ActivityManager`**: Manages activity lifecycles and back-stacks.
- **`ContentProvider`**: Enables data encapsulation and cross-app data sharing.
- **`View System`**: Provides standard UI widgets (Buttons, TextViews, Layouts).
- **`NotificationManager`**: Displays status bar alerts.

#### B. Relation of Framework Layer to SQLite Database (4 Marks):
- **High-Level Data Wrappers**: The Framework layer provides the Java classes (`SQLiteOpenHelper`, `SQLiteDatabase`, `ContentValues`, `Cursor`) that wrap the native C-library calls.
- **Data Sharing Abstraction**: Integrates SQLite databases with **`ContentProvider`** components, allowing database records to be queried securely across apps using `ContentResolver`.
