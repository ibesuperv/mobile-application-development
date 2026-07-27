# Question Paper Solutions: Broadcasts, Services & Firebase Paper (Part A & Part B)

---

## Part A – Quiz Solutions (2 Marks Each)

### 1. What are Broadcasts? Give examples. (2 Marks)
- **Definition**: System-wide or application-level message announcements dispatched via Intents that components can listen for and respond to.
- **Examples**: `Intent.ACTION_POWER_CONNECTED` (charger plugged in), `Intent.ACTION_BATTERY_LOW`, `Intent.ACTION_BOOT_COMPLETED`.

---

### 2. What are the different ways of sending broadcasts? (2 Marks)
1. **`sendBroadcast(Intent)`**: Dispatches an asynchronous, unordered broadcast to all matching receivers simultaneously.
2. **`sendOrderedBroadcast(Intent, String)`**: Dispatches a sequential broadcast to receivers one by one based on priority, allowing higher-priority receivers to abort or modify it.
3. **`LocalBroadcastManager.sendBroadcast(Intent)`**: Dispatches a broadcast private to the hosting app process.

---

### 3. List the two types of Hierarchical Navigation. (2 Marks)
1. **Ancestral (Up) Navigation**: Navigates up the app's internal screen hierarchy to the parent Activity (configured via `android:parentActivityName`).
2. **Temporal (Back) Navigation**: Navigates backwards through the history of recently visited screens stored in the system task Back Stack.

---

### 4. Give examples of Mobile Application based Services (At least 4). (2 Marks)
1. **Location Tracking Service**: Tracks real-time GPS coordinates in the background.
2. **Audio/Music Streaming Service**: Plays media in the background via a Foreground Service.
3. **File Download Manager Service**: Downloads large files off the main thread.
4. **Data Sync Service**: Synchronizes local database records with cloud servers in the background.

---

### 5. Are simulators different from emulators? List two differences. (2 Marks)
**Yes, they are different.**
1. **Hardware Virtualization**: Emulators emulate target hardware architecture (CPU registers, GPU, memory) as well as the OS (e.g. QEMU Android Emulator); Simulators simulate software UI/OS behavior without hardware virtualization (e.g. iOS Simulator).
2. **Execution Speed & Fidelity**: Simulators execute faster on host PCs but lower fidelity; Emulators offer 100% binary fidelity to real hardware but require heavier hardware resources.

---

## Part B Solutions

### Question 1(a): Working of Toast in Android (5 Marks)
Detailed specification of parameters (`context`, `text`, `duration`) and methods (`makeText()`, `show()`, `setGravity()`) with Java code snippet available in [15_Question_Paper_Solutions_Extra.md](file:///v:/ACADEMIC/6TH%20SEM/MOBILE/NOTES/UNIT-1/15_Question_Paper_Solutions_Extra.md#question-3a-working-of-toast-in-android-parameters--methods-5-marks).

---

### Question 1(b): Importance of Mobile Applications & Why Develop for Android (5 Marks)
Detailed explanation of 24/7 accessibility, sensor capabilities, global market share (~70%+), open-source AOSP, and Google Services available in [15_Question_Paper_Solutions_Extra.md](file:///v:/ACADEMIC/6TH%20SEM/MOBILE/NOTES/UNIT-1/15_Question_Paper_Solutions_Extra.md#question-2a-importance-of-mobile-applications--why-develop-for-android-5-marks).

---

### Question 2(a): Differentiate Between the Two Types of Intents (4 Marks)

| Dimension | Explicit Intents | Implicit Intents |
| :--- | :--- | :--- |
| **Component Specification** | Explicitly defines target component class name (`TargetActivity.class`). | Does NOT specify class name; specifies general action (`ACTION_VIEW`). |
| **Target Application** | Used to navigate between activities within the **same application**. | Used to trigger matching external applications on the device (Browser, Camera). |
| **Security** | Secure internal navigation; no external component interception. | Requires resolution check (`resolveActivity()`) before launching to avoid crashes. |

---

### Question 2(b): Java Code for Different Types of Intents with Examples (6 Marks)

#### 1. Explicit Intent Java Code (Navigating to internal Activity):
```java
// Explicit Intent targeting internal SecondActivity.class
Intent explicitIntent = new Intent(MainActivity.this, SecondActivity.class);
explicitIntent.putExtra("USER_ID", 101); // Passing Extra parameter
startActivity(explicitIntent);
```

#### 2. Implicit Intent Java Code (Opening Web Browser):
```java
// Implicit Intent requesting ACTION_VIEW with Web URI
Intent implicitIntent = new Intent(Intent.ACTION_VIEW);
implicitIntent.setData(Uri.parse("https://developer.android.com"));

// Defensive check before launching
if (implicitIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(implicitIntent);
} else {
    Toast.makeText(this, "No browser application found!", Toast.LENGTH_SHORT).show();
}
```

---

## Question 3: Power Connection Broadcast Receiver Implementation (Java & Manifest) (10 Marks)

App displaying a Toast message when the device is connected to a power source.

### A. Java Broadcast Receiver Subclass (`PowerConnectionReceiver.java`)
```java
package com.example.powerapp;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.widget.Toast;

public class PowerConnectionReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent != null && Intent.ACTION_POWER_CONNECTED.equals(intent.getAction())) {
            Toast.makeText(context, "Device Connected to Power Source!", Toast.LENGTH_LONG).show();
        }
    }
}
```

---

### B. Manifest Configuration (`AndroidManifest.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.powerapp">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="Power App"
        android:theme="@style/Theme.AppCompat.Light">

        <!-- Static Broadcast Receiver Registration -->
        <receiver
            android:name=".PowerConnectionReceiver"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
            </intent-filter>
        </receiver>

        <!-- MainActivity -->
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

## Question 4: Life Cycle of Different Forms of Services (10 Marks)

Services operate in two forms: **Started Services** and **Bound Services**.

```
       STARTED SERVICE LIFECYCLE                   BOUND SERVICE LIFECYCLE
       +-----------------------+                   +---------------------+
       |    startService()     |                   |    bindService()    |
       +-----------+-----------+                   +----------+----------+
                   │                                          │
                   v                                          v
               onCreate()                                 onCreate()
                   │                                          │
            onStartCommand()                               onBind()
                   │                                          │
           [ Service Running ]                     [ Client Bound to Service ]
                   │                                          │
            stopSelf() / stopService()                   unbindService()
                   │                                          │
                   v                                          v
              onDestroy()                                 onUnbind() ──> onDestroy()
```

### 1. Started Service Lifecycle Callbacks:
- **`onCreate()`**: First creation callback; initializes resources.
- **`onStartCommand(Intent, flags, startId)`**: Triggered when client calls `startService()`. Executes background work. Returns flags (`START_STICKY`, `START_NOT_STICKY`).
- **`onDestroy()`**: Called when service is stopped via `stopSelf()` or `stopService()`.

### 2. Bound Service Lifecycle Callbacks:
- **`onCreate()`**: First creation callback.
- **`onBind(Intent)`**: Triggered when client calls `bindService()`. Returns an **`IBinder`** interface for IPC.
- **`onUnbind(Intent)`**: Called when all client components unbind via `unbindService()`.
- **`onDestroy()`**: Final teardown phase.

---

## Question 5: Firebase & AdMob, Polish & Publish (10 Marks)

### i. Firebase and AdMob (5 Marks)
- **Firebase**: Backend-as-a-Service (BaaS) suite by Google providing Realtime Database, Cloud Firestore, Authentication, Cloud Messaging, and Analytics.
- **AdMob**: Google's mobile advertising platform allowing developers to monetize Android apps by serving banner, interstitial, and rewarded video ads linked to Firebase Analytics.

### ii. Polish and Publish (5 Marks)
- **Polishing**: Removing debug logs (`Log.d`), setting `versionCode` & `versionName` in `build.gradle`, and optimizing resources via ProGuard/R8.
- **Publishing**: Generating signed Android App Bundle (`.aab`) using a Keystore (`.jks`), creating Google Play Console account ($25 fee), setting store listing assets & Privacy Policy URL, and submitting to production track.
