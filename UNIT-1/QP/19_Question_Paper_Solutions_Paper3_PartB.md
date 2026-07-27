# Question Paper Solutions: Paper-3 Part-B (Questions 1a to 5)

---

## Question 1a: Importance of Activity Life Cycle & Different Phases (6 Marks)

### A. Importance of the Activity Life Cycle (2 Marks)
Understanding the Activity Life Cycle is mandatory because mobile apps execute in a resource-constrained environment where the system can pause, stop, or kill activities at any time due to incoming phone calls, device rotation, or low memory. Proper lifecycle management prevents:
- **Memory leaks** (retaining unneeded references when activity is destroyed).
- **Data loss** (losing user input on screen rotation or app switching).
- **App crashes** (attempting to update UI elements when the activity is dead).

---

### B. Phases of Activity Life Cycle with Diagram (4 Marks)

```
                            ACTIVITY LIFECYCLE PHASES
                                        │
                                    onCreate()
                                        │
                                    onStart()
                                        │
                                    onResume()
                                        │
                            [ Resumed / Active State ]
                             (User interaction active)
                                        │
                                    onPause()
                                        │
                                     onStop()
                                        │
                                   onDestroy()
```

1. **Created (`onCreate`)**: Initial setup phase; layout inflated via `setContentView()`.
2. **Visible (`onStart`)**: Activity becomes visible on screen.
3. **Resumed (`onResume`)**: Activity gains user focus; active foreground interaction phase.
4. **Paused (`onPause`)**: Activity is partially obscured (e.g. dialog pop-up); saves uncommitted data.
5. **Stopped (`onStop`)**: Activity is completely hidden from view.
6. **Destroyed (`onDestroy`)**: Final teardown phase; memory released.

---

## Question 1b: Features of Android that Help in Developing Mobile Apps (4 Marks)

1. **Rich Application Framework**: Reusable components (`Activity`, `Service`, `ContentProvider`, `BroadcastReceiver`).
2. **Open Source & Extensible (AOSP)**: Apache-licensed source code allowing deep customization.
3. **Built-in SQLite Database**: High-performance lightweight embedded database for structured local data storage.
4. **Integrated Google Services**: Seamless access to Google Maps, Firebase Cloud Messaging, Location APIs, and Google Analytics.

---

## Question 2a: Discuss Various Components of Android Application in Detail (6 Marks)

The Android framework defines **4 Core Application Components**:

```
                         4 CORE APPLICATION COMPONENTS
                                       │
     +-------------------+-------------+-------------+-------------------+
     |                   |                           |                   |
     v                   v                           v                   v
 ACTIVITIES          SERVICES                BROADCAST RECEIVERS   CONTENT PROVIDERS
 (UI Screens)        (Background Ops)        (System Listener)     (Data Sharing)
```

1. **Activities**: UI screens with user interaction capabilities (`View` hierarchy).
2. **Services**: Long-running background operations without a UI (`startService()` / `bindService()`).
3. **Broadcast Receivers**: Intercepts system/app broadcast events (`registerReceiver()`).
4. **Content Providers**: Exposes standard CRUD interface for data sharing using URIs.

---

## Question 2b: Need for AVD & Step-by-Step Procedure to Create a New AVD (4 Marks)

### A. Need for AVD (Android Virtual Device) (1.5 Marks)
An AVD allows developers to test apps across thousands of screen sizes, resolutions, density buckets, and Android API levels without buying physical hardware devices.

---

### B. Step-by-Step Procedure to Create a New AVD (2.5 Marks)
1. In Android Studio, open **Tools > Device Manager** (or click the Device Manager icon).
2. Click **Create Virtual Device**.
3. Select a **Hardware Profile** (e.g., Pixel 7) specifying screen resolution and click **Next**.
4. Select a **System Image** (e.g. Android 13 / API level 33 `x86_64`) and click **Download** if not present, then click **Next**.
5. Verify AVD configuration name and click **Finish**.
6. Click the **Play (Run)** icon next to the created AVD to launch the emulator.

---

## Question 3a: Android Architecture Diagram & Layers (5 Marks)

```
                         ANDROID ARCHITECTURE STACK
+-----------------------------------------------------------------------+
| APPLICATIONS LAYER (Launcher, Contacts, Browser, Custom User Apps)    |
+-----------------------------------------------------------------------+
| APPLICATION FRAMEWORK LAYER (ActivityManager, ContentProvider, View)  |
+-----------------------------------------------------------------------+
| NATIVE LIBRARIES LAYER (SQLite, WebKit) | ANDROID RUNTIME (ART/Core)   |
+-----------------------------------------------------------------------+
| LINUX KERNEL LAYER (Display, Camera, Wi-Fi, Memory, Power Drivers)    |
+-----------------------------------------------------------------------+
```

- **Linux Kernel**: Hardware driver abstraction, memory & power management.
- **Native Libraries & ART**: C/C++ engine libraries (`SQLite`, `WebKit`) + Java bytecode execution engine.
- **Application Framework**: High-level Java APIs for developers.
- **Applications**: Top-level user apps.

---

## Question 3b: Purpose & Categorization of Intents (5 Marks)

### A. Purpose of Intents (2 Marks)
An **Intent** is an asynchronous message object used to trigger operations in external/internal Activities, Services, or Broadcast Receivers, facilitating data passing.

---

### B. Categorization of Intents (3 Marks)

1. **Explicit Intents**:
   - Specifies target component by class name. Used inside the *same app*.
   - `startActivity(new Intent(this, TargetActivity.class));`
2. **Implicit Intents**:
   - Specifies general action and data URI. System resolves matching *external app*.
   - `startActivity(new Intent(Intent.ACTION_VIEW, Uri.parse("https://google.com")));`

---

## Question 4: Case Study App: 10s Splash Launcher Passing Username Data to Second Activity & Receiving Data Back (10 Marks)

App Workflow:
1. `LauncherActivity`: Displays "WELCOME TO MOBILE APP DEVELOPMENT" vertically for **10 seconds**, then automatically opens `SecondActivity`.
2. `SecondActivity`: Asks for username input. On clicking "Submit", returns the username back to `LauncherActivity`.
3. `LauncherActivity`: Receives username data in `onActivityResult` and displays `"WELCOME [Username]"`.

---

### A. Launcher Activity Layout (`activity_launcher.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="24dp">

    <TextView
        android:id="@+id/tv_welcome_msg"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="WELCOME TO MOBILE APP DEVELOPMENT"
        android:textSize="20sp"
        android:textStyle="bold"
        android:gravity="center" />

</LinearLayout>
```

---

### B. Launcher Activity Java Code (`LauncherActivity.java`)
```java
package com.example.datareturnapp;

import android.content.Intent;
import android.os.Bundle;
import android.os.Handler;
import android.widget.TextView;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;

public class LauncherActivity extends AppCompatActivity {

    private static final int REQUEST_CODE_USER = 101;
    private TextView mTvWelcome;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_launcher);

        mTvWelcome = findViewById(R.id.tv_welcome_msg);

        // Handler to delay opening SecondActivity by 10 seconds (10000 ms)
        new Handler().postDelayed(() -> {
            Intent intent = new Intent(LauncherActivity.this, SecondActivity.class);
            startActivityForResult(intent, REQUEST_CODE_USER);
        }, 10000);
    }

    // Receive returned data from SecondActivity
    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if (requestCode == REQUEST_CODE_USER && resultCode == RESULT_OK && data != null) {
            String username = data.getStringExtra("USERNAME_KEY");
            mTvWelcome.setText("WELCOME " + username.toUpperCase());
        }
    }
}
```

---

### C. Second Activity Layout (`activity_second.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="24dp">

    <EditText
        android:id="@+id/et_username"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter Username" />

    <Button
        android:id="@+id/btn_submit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="SUBMIT"
        android:layout_marginTop="20dp" />

</LinearLayout>
```

---

### D. Second Activity Java Code (`SecondActivity.java`)
```java
package com.example.datareturnapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import androidx.appcompat.app.AppCompatActivity;

public class SecondActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        EditText etUsername = findViewById(R.id.et_username);
        Button btnSubmit = findViewById(R.id.btn_submit);

        btnSubmit.setOnClickListener(v -> {
            String username = etUsername.getText().toString().trim();

            // Pass result back to LauncherActivity
            Intent returnIntent = new Intent();
            returnIntent.putExtra("USERNAME_KEY", username);
            setResult(RESULT_OK, returnIntent);
            finish(); // Close SecondActivity
        });
    }
}
```

---

### E. Manifest File (`AndroidManifest.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.datareturnapp">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="Data Return App"
        android:theme="@style/Theme.AppCompat.Light">

        <!-- Launcher Activity -->
        <activity
            android:name=".LauncherActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!-- Second Activity -->
        <activity
            android:name=".SecondActivity"
            android:exported="false" />

    </application>
</manifest>
```
