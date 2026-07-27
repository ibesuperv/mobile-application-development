# Question Paper Solutions: Part-B (Questions 1a to 5)

---

## Question 1a: Hierarchy of Views & View Groups and Detail on Single-Row/Column ViewGroup (6 Marks)

### A. Hierarchy of Views & View Groups Diagram (3 Marks)

In Android UI design, the interface is structured as a **Tree Hierarchy** of `View` and `ViewGroup` objects.

```
                           VIEW HIERARCHY TREE
                                    │
                                ViewGroup
                           (Root Layout / e.g. LinearLayout)
                                    │
       +----------------------------+----------------------------+
       │                                                         │
     View                                                    ViewGroup
(Widget e.g. TextView)                                  (Nested Sub-Layout)
                                                                 │
                                                    +------------+------------+
                                                    │                         │
                                                  View                      View
                                            (Button Widget)           (EditText Widget)
```

- **`View`**: Base UI widget drawing pixels on screen and handling user input events (e.g. `TextView`, `Button`).
- **`ViewGroup`**: Invisible container extending `View` that holds and arranges child Views.

---

### B. ViewGroup Arranging Views in Single Row or Column: `LinearLayout` (3 Marks)

**`LinearLayout`** is the primary `ViewGroup` that arranges all child views in a **single linear direction** (either horizontally in a single row or vertically in a single column).

- **Attribute**: `android:orientation="vertical"` or `android:orientation="horizontal"`.
- **Weighted Distribution**: Uses `android:layout_weight` alongside `0dp` width/height to distribute remaining screen space proportionally among child views.

---

## Question 1b: Develop Layout XML for Wrap-Content EditText and Button (4 Marks)

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="16dp">

    <!-- EditText width set to wrap_content to fit content -->
    <EditText
        android:id="@+id/et_input"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:hint="Enter text" />

    <!-- Button width set to wrap_content to fit content -->
    <Button
        android:id="@+id/btn_send"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send" />

</LinearLayout>
```

---

## Question 2: Android Architecture Stack (Diagram & 4 Main Layers) (10 Marks)

The Android OS Software Stack consists of **4 Main Layers** operating over 5 sections:

```
                         ANDROID ARCHITECTURE STACK
+-----------------------------------------------------------------------+
| Layer 4: APPLICATIONS LAYER                                           |
|          (System Apps & User Installed Applications)                  |
+-----------------------------------------------------------------------+
| Layer 3: APPLICATION FRAMEWORK LAYER                                  |
|          (ActivityManager, ContentProvider, LocationManager, View)    |
+-----------------------------------------------------------------------+
| Layer 2: NATIVE LIBRARIES LAYER    |   ANDROID RUNTIME (ART)          |
|          (SQLite, WebKit, OpenGL)  |   (Core Libraries + AOT/JIT)     |
+-----------------------------------------------------------------------+
| Layer 1: LINUX KERNEL LAYER                                           |
|          (Display, Camera, Wi-Fi, Flash Memory, Power Drivers)        |
+-----------------------------------------------------------------------+
```

### Detailed Layer Breakdown:

1. **Linux Kernel Layer (Layer 1)**:
   - Provides hardware driver abstraction (Display, Camera, Audio, Memory), process isolation, security, and power management.
2. **Native Libraries & Android Runtime Layer (Layer 2)**:
   - **C/C++ Libraries**: `SQLite` (database), `WebKit` (browser rendering), `OpenGL ES` (3D graphics), `Surface Manager`.
   - **Android Runtime (ART)**: Executes `.dex` bytecode using AOT/JIT compilation and handles garbage collection.
3. **Application Framework Layer (Layer 3)**:
   - Exposes high-level Java APIs for developers (`ActivityManager`, `ContentProvider`, `NotificationManager`, `View System`).
4. **Applications Layer (Layer 4)**:
   - The top layer containing user-facing native applications (Launcher, Contacts, Phone, and Play Store apps).

---

## Question 3a: Phases of Activity Life Cycle with Diagram (5 Marks)

```
                               onCreate()
                                   │
                               onStart()
                                   │
                               onResume()
                                   │
                           [ Running / Resumed ]
                                   │
                               onPause()
                                   │
                                onStop()
                                   │
                              onDestroy()
```

1. **`onCreate()`**: First creation callback; initializes views and inflates XML (`setContentView()`).
2. **`onStart()`**: Activity becomes visible on screen.
3. **`onResume()`**: Activity gains user focus and becomes interactive.
4. **`onPause()`**: Activity loses focus (partially covered by dialog); saves transient data.
5. **`onStop()`**: Activity is completely hidden from view.
6. **`onDestroy()`**: Final teardown callback before activity is removed from RAM.

---

## Question 3b: Categorize & Discuss Panes of Android Studio Window (5 Marks)

```
+-----------------------------------------------------------------------+
| 1. Toolbar / Menu Bar                                                 |
+-------------------+-------------------------------+-------------------+
|                   |                               |                   |
| 2. Project Tool   | 3. Editor Window              | 4. Component Tree /|
|    Window (Pane)  |    (Code / Layout View)       |    Attributes     |
|                   |                               |                   |
+-------------------+-------------------------------+-------------------+
| 5. Terminal / Logcat / Build / Run Tool Window Pane                   |
+-----------------------------------------------------------------------+
```

1. **Project Tool Window (Left Pane)**: Displays project folder structure (`app/java`, `app/res`, `Gradle Scripts`).
2. **Editor Window (Center Pane)**: Primary workspace to edit Java/Kotlin code or visually design XML layouts.
3. **Tool Bar / Navigation Bar (Top Pane)**: Contains shortcuts to run/debug apps, select target emulators, and open SDK Manager.
4. **Component Tree & Attributes Pane (Right Pane)**: Displays UI view hierarchies and lets developers edit widget properties (margins, text size).
5. **Bottom Status/Logcat Pane (Bottom Pane)**: Displays system execution logs (`Logcat`), terminal prompt, build progress, and test output.

---

## Question 4a: Different Ways of Running an Android App (Step-by-Step) (6 Marks)

### Method 1: Running on an Emulator (AVD - Android Virtual Device)
1. In Android Studio, open **Tools > Device Manager**.
2. Click **Create Device**, select a hardware profile (e.g. Pixel 6), download an Android System Image (API 33), and click **Finish**.
3. Select the created emulator from the target device drop-down menu on the top toolbar.
4. Click the green **Run (`Shift + F10`)** button to compile, deploy, and launch the APK.

---

### Method 2: Running on a Physical Android Device
1. On the Android device, go to **Settings > About Phone** and tap **Build Number 7 times** to unlock Developer Options.
2. Open **Settings > Developer Options** and enable **USB Debugging**.
3. Connect device to PC via USB cable and authorize the computer prompt (`Allow USB debugging`).
4. Select physical device from Android Studio toolbar drop-down and click **Run (`Shift + F10`)**.

---

## Question 4b: Methods for Activating Each Component Type (4 Marks)

| Component Type | Activation Method | Code Example |
| :--- | :--- | :--- |
| **Activity** | `startActivity(Intent)` or `startActivityForResult(Intent, code)` | `startActivity(new Intent(this, SecondActivity.class));` |
| **Service** | `startService(Intent)`, `startForegroundService()`, or `bindService()` | `startService(new Intent(this, MyService.class));` |
| **Broadcast Receiver** | `sendBroadcast(Intent)` or `sendOrderedBroadcast()` | `sendBroadcast(new Intent("com.example.CUSTOM_ACTION"));` |
| **Content Provider** | `ContentResolver.query()`, `insert()`, `update()`, `delete()` | `getContentResolver().query(uri, null, null, null, null);` |

---

## Question 5: Two-Activity Message Passing App (Complete XML, Java & Manifest) (10 Marks)

App passing a message typed in Activity 1 to Activity 2.

---

### A. Activity 1 Layout (`activity_main.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="16dp">

    <EditText
        android:id="@+id/et_message"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Type a message" />

    <Button
        android:id="@+id/btn_send"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send"
        android:layout_marginTop="16dp" />

</LinearLayout>
```

---

### B. Activity 1 Java Code (`MainActivity.java`)
```java
package com.example.twoactivityapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    public static final String EXTRA_MESSAGE = "com.example.twoactivityapp.MESSAGE";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        EditText etMessage = findViewById(R.id.et_message);
        Button btnSend = findViewById(R.id.btn_send);

        btnSend.setOnClickListener(v -> {
            String message = etMessage.getText().toString();
            Intent intent = new Intent(MainActivity.this, SecondActivity.class);
            intent.putExtra(EXTRA_MESSAGE, message);
            startActivity(intent);
        });
    }
}
```

---

### C. Activity 2 Layout (`activity_second.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="16dp">

    <TextView
        android:id="@+id/tv_received_message"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="22sp"
        android:textColor="#000000" />

</LinearLayout>
```

---

### D. Activity 2 Java Code (`SecondActivity.java`)
```java
package com.example.twoactivityapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class SecondActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        TextView tvMessage = findViewById(R.id.tv_received_message);

        Intent intent = getIntent();
        if (intent != null && intent.hasExtra(MainActivity.EXTRA_MESSAGE)) {
            String message = intent.getStringExtra(MainActivity.EXTRA_MESSAGE);
            tvMessage.setText(message);
        }
    }
}
```

---

### E. Manifest File (`AndroidManifest.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.twoactivityapp">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="Two Activity App"
        android:theme="@style/Theme.AppCompat.Light">

        <!-- Activity 1 (Launcher) -->
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!-- Activity 2 -->
        <activity
            android:name=".SecondActivity"
            android:exported="false"
            android:parentActivityName=".MainActivity" />

    </application>
</manifest>
```
