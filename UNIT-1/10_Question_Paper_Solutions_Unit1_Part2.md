# Question Paper Solutions (Part 3): Core Concepts, App Design & Architecture

---

## Question 13: In Detail Explain Open Handset Alliance (OHA) (10 Marks)

### A. What is Open Handset Alliance (OHA)? (3 Marks)
The **Open Handset Alliance (OHA)** is a business alliance of 84+ technology and mobile companies established on **November 5, 2007**, led by **Google**. Its primary goal was to develop **open standards for mobile devices** and release **Android** as a free, open-source mobile operating system under the **Apache Open Source License**.

```
                        OPEN HANDSET ALLIANCE (OHA)
                                     |
    +-------------------+------------+------------+-------------------+
    |                   |                         |                   |
    v                   v                         v                   v
MOBILE OPERATORS    HANDSET MANUFACTURERS     SEMICONDUCTOR CO.   SOFTWARE CO.
(T-Mobile, NTT      (Samsung, HTC,            (Qualcomm, Intel,   (Google, eBay,
 Docomo, Sprint)     Motorola, Sony)           Nvidia, MediaTek)   Wipro)
```

---

### B. The 4 Pillar Member Categories of OHA (4 Marks)

1. **Software Companies**: Led by **Google**, providing software innovation, application frameworks, and cloud integration.
2. **Handset Manufacturers**: Companies like **Samsung, HTC, Motorola, Sony, LG** that build smartphones running the Android OS.
3. **Semiconductor Companies**: Chipmakers like **Qualcomm, Broadcom, Intel, Nvidia, MediaTek** ensuring Android support on ARM and x86 hardware.
4. **Mobile Network Operators**: Carriers like **T-Mobile, NTT Docomo, Sprint, Telefónica** providing network deployment and distribution.

---

### C. Key Objectives & Impact of OHA (3 Marks)
- **Fostering Openness**: Ended proprietary mobile OS dominance (like Symbian or Windows Mobile) by offering Android as Open Source (AOSP).
- **Lower Costs**: Reduced device manufacturing costs by removing software licensing fees.
- **Developer Ecosystem**: Standardized Android APIs, enabling developers to write apps that run across thousands of hardware models.

---

## Question 14: Design an App to Display Your NAME, USN and Mention the Files (10 Marks)

### A. Layout Design XML (`res/layout/activity_main.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="16dp">

    <TextView
        android:id="@+id/tv_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Name: VARUN"
        android:textSize="22sp"
        android:textStyle="bold"
        android:textColor="#000000" />

    <TextView
        android:id="@+id/tv_usn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="USN: 1AB21CS001"
        android:textSize="18sp"
        android:layout_marginTop="12dp"
        android:textColor="#555555" />

</LinearLayout>
```

---

### B. Java Implementation (`MainActivity.java`)
```java
package com.example.myinfoapp;

import android.os.Bundle;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    private TextView mNameTextView;
    private TextView mUsnTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Bind layout elements
        mNameTextView = findViewById(R.id.tv_name);
        mUsnTextView = findViewById(R.id.tv_usn);

        // Set dynamic text values
        mNameTextView.setText("Name: VARUN");
        mUsnTextView.setText("USN: 1AB21CS001");
    }
}
```

---

### C. App Layout Visual Mockup Design
```
+------------------------------------------+
|  My Info App                             |
+------------------------------------------+
|                                          |
|                                          |
|              Name: VARUN                 |
|            USN: 1AB21CS001               |
|                                          |
|                                          |
+------------------------------------------+
```

---

### D. Manifest Declaration (`AndroidManifest.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myinfoapp">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="My Info App"
        android:theme="@style/Theme.AppCompat.Light">

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

## Question 15: Define the Following with Example (10 Marks)

1. **Views (2 Marks)**:
   - *Definition*: A `View` is the basic UI building block representing a rectangular area on the screen responsible for drawing and event handling.
   - *Example*: `TextView`, `Button`, `ImageView`.
2. **Groups (`ViewGroup`) (2 Marks)**:
   - *Definition*: A `ViewGroup` is a invisible container subclass of `View` that holds other child Views and manages their screen layout geometry.
   - *Example*: `LinearLayout`, `RelativeLayout`, `ConstraintLayout`.
3. **Gradle (2 Marks)**:
   - *Definition*: An advanced build automation tool used by Android Studio to compile source code, resolve external library dependencies, and package resources into an executable APK/AAB bundle.
   - *Example*: Adding dependencies in `build.gradle`: `implementation 'com.google.android.material:material:1.9.0'`.
4. **SDK Manager (2 Marks)**:
   - *Definition*: A tool inside Android Studio used to download, update, and manage Android SDK platform versions, build-tools, system images for emulators, and documentation.
   - *Example*: Downloading `Android 13.0 (API Level 33)` SDK Platform package.
5. **Notifications (2 Marks)**:
   - *Definition*: A message displayed outside your application's normal UI in the status bar and drawer to alert users to events.
   - *Example*: `NotificationCompat.Builder builder = new NotificationCompat.Builder(this, "CHANNEL_ID").setContentTitle("Alert");`.

---

## Question 16: What is a Virtual Machine? Differentiate Between Dalvik VM and JVM (10 Marks)

### A. What is a Virtual Machine? (2 Marks)
A **Virtual Machine (VM)** is a software-based abstraction of a physical computer that executes programs like a real hardware machine, isolating application code from underlying hardware.

---

### B. Dalvik Virtual Machine (DVM) vs. Java Virtual Machine (JVM) (8 Marks)

```
       JAVA VIRTUAL MACHINE (JVM)             DALVIK VIRTUAL MACHINE (DVM)
     +----------------------------+         +----------------------------+
     | Stack-Based Architecture   |         | Register-Based Architecture|
     | Executes Java Bytecode     |         | Executes Dalvik Bytecode   |
     | (.class files inside .jar) |         | (.dex executable file)     |
     +----------------------------+         +----------------------------+
```

| Feature Dimension | Java Virtual Machine (JVM) | Dalvik Virtual Machine (DVM) |
| :--- | :--- | :--- |
| **Architecture Type** | **Stack-based** (Pushes/pops data from memory stack; requires more instructions). | **Register-based** (Uses CPU registers directly; requires fewer instructions and executes faster). |
| **Executable Bytecode** | Executes `.class` Java bytecode files. | Executes **`.dex` (Dalvik Executable)** bytecode files. |
| **Memory Footprint** | Large memory footprint designed for desktop/server CPUs. | Highly optimized for mobile devices with **limited RAM and battery power**. |
| **File Bundling** | Multiple `.class` files bundled into `.jar` archives. | Multiple `.class` files compiled and compressed into a single `classes.dex` file inside an `.apk`. |

---

## Question 17: Explain Android Activity Life Cycle (10 Marks)

An **Activity** represents a single screen with a UI. The system manages activities using a **lifecycle stack** through 7 core callback methods:

```
                                  onCreate()
                                      │
                                  onStart()
                                      │
                                  onResume()
                                      │
                              [ Activity Running ]
                                      │
                                  onPause()
                                      │
                                  onStop()
                                      │
                                 onDestroy()
```

### The 7 Lifecycle Callback Methods:

1. **`onCreate()` (Mandatory)**: Called when the activity is first created. Used to perform static setup (e.g. `setContentView()`, initializing views).
2. **`onStart()`**: Called when the activity becomes **visible** to the user.
3. **`onResume()`**: Called when the activity gains **user focus** and enters the foreground. The activity is now interactable.
4. **`onPause()`**: Called when another activity gains focus (partially obscured, like a dialog popup). Used to commit unsaved edits or stop animations.
5. **`onStop()`**: Called when the activity is **no longer visible** to the user (completely hidden behind another full-screen activity).
6. **`onRestart()`**: Called when an activity in the stopped state is about to be navigated back to by the user.
7. **`onDestroy()`**: Called before the activity is completely destroyed from memory (either by calling `finish()` or system memory reclamation).
