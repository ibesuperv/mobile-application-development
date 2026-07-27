# Question Paper Solutions: Paper-4 Part-B (Questions 1a to 5)

---

## Question 1a: Importance of Activity Life Cycle & Different Phases (6 Marks)

### A. Importance of Activity Life Cycle (2 Marks)
The Activity Life Cycle is crucial because mobile operating systems continuously manage system resources, pausing, stopping, or killing apps based on user navigation, incoming calls, or low RAM memory. Proper lifecycle handling prevents:
- **Memory leaks** (retaining unused references when activities die).
- **Data loss** (losing unsaved form input on screen rotation).
- **Crashing** (updating UI elements when activity is no longer active).

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

1. **Created (`onCreate`)**: Initial creation phase; layout inflated via `setContentView()`.
2. **Visible (`onStart`)**: Activity becomes visible on screen.
3. **Resumed (`onResume`)**: Activity gains user focus and foreground interaction.
4. **Paused (`onPause`)**: Activity is partially obscured (e.g. dialog pop-up); saves uncommitted data.
5. **Stopped (`onStop`)**: Activity is completely hidden from view.
6. **Destroyed (`onDestroy`)**: Teardown phase; memory released.

---

## Question 1b: Features of Android Helping Mobile App Development (4 Marks)

1. **Rich Framework Components**: Standard building blocks (`Activity`, `Service`, `ContentProvider`, `BroadcastReceiver`).
2. **Open Source & Extensible (AOSP)**: Apache-licensed source code allowing complete customization.
3. **Built-in SQLite Database**: Lightweight relational database for structured local storage.
4. **Integrated Google Services**: Seamless access to Google Maps, Firebase Cloud Messaging, and Location APIs.

---

## Question 2a: Quote Examples & Elucidate Various Components of Android Application (6 Marks)

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

1. **Activities** (e.g., `MainActivity.class` displaying home UI):
   - A single interactive UI screen built using a View hierarchy.
2. **Services** (e.g., `MusicPlaybackService.class` streaming audio in background):
   - A component running long operations in the background without UI.
3. **Broadcast Receivers** (e.g., `PowerReceiver.class` listening for low battery):
   - Intercepts and responds to system-wide or app-specific broadcast intents.
4. **Content Providers** (e.g., `ContactsProvider` sharing system contact list):
   - Manages and shares application data with external apps via URIs.

---

## Question 2b: Procedure to Create an Options Menu with Code Sample (4 Marks)

### Step-by-Step Procedure:
1. Create a menu XML file in `res/menu/options_menu.xml`.
2. Override **`onCreateOptionsMenu(Menu menu)`** in Activity to inflate the menu resource using `getMenuInflater().inflate()`.
3. Override **`onOptionsItemSelected(MenuItem item)`** to handle menu item click events.

#### 1. Menu Resource File (`res/menu/options_menu.xml`):
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/action_settings"
        android:title="Settings"
        app:showAsAction="never" />
    <item
        android:id="@+id/action_about"
        android:title="About Us"
        app:showAsAction="never" />
</menu>
```

#### 2. Java Menu Implementation (`MainActivity.java`):
```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.options_menu, menu);
    return true;
}

@Override
public boolean onOptionsItemSelected(MenuItem item) {
    int id = item.getItemId();
    if (id == R.id.action_settings) {
        Toast.makeText(this, "Settings Selected", Toast.LENGTH_SHORT).show();
        return true;
    } else if (id == R.id.action_about) {
        Toast.makeText(this, "About Us Selected", Toast.LENGTH_SHORT).show();
        return true;
    }
    return super.onOptionsItemSelected(item);
}
```

---

## Question 3a: Android Architecture Diagram & Layers (4 Marks)

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

## Question 3b: Purpose of Intents & Categorization with Case Study (6 Marks)

### A. Case Study Scenario: E-Commerce Shopping App
In a shopping app (`ShopApp`):
- Clicking a item opens `ProductDetailActivity` inside the app (**Explicit Intent**).
- Clicking "Share Link" triggers external sharing apps like WhatsApp or Email (**Implicit Intent**).

---

### B. Categorization & Code Examples:

1. **Explicit Intents**:
   - Used to launch a specific component within the *same application*.
   - `startActivity(new Intent(this, ProductDetailActivity.class));`
2. **Implicit Intents**:
   - Used to request an action from *any external app* that matches the requested action.
   - `startActivity(new Intent(Intent.ACTION_VIEW, Uri.parse("https://google.com")));`

---

## Question 4: Case Study App: 10s Splash Launcher Passing Username Data Back & Forth (10 Marks)

App Workflow:
1. `LauncherActivity`: Displays "WELCOME TO MOBILE APP DEVELOPMENT" vertically for **10 seconds**, then automatically calls `SecondActivity`.
2. `SecondActivity`: Asks for username input. On clicking "Submit", returns username data back to `LauncherActivity`.
3. `LauncherActivity`: Receives username data in `onActivityResult` and displays `"WELCOME [Username]"`.

### Implementation Code Reference:
- Layout XML, Java Code, and Manifest definitions are fully detailed in [19_Question_Paper_Solutions_Paper3_PartB.md](file:///v:/ACADEMIC/6TH%20SEM/MOBILE/NOTES/UNIT-1/19_Question_Paper_Solutions_Paper3_PartB.md#question-4-case-study-app-10s-splash-launcher-passing-username-data-to-second-activity--receiving-data-back-10-marks).

---

## Question 5: Counter App UI Design & Click Handler Code (10 Marks)

App displaying a counter TextView and a **COUNT** button that increments the counter value on each click.

### A. Layout File (`activity_main.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="24dp">

    <!-- Counter Display TextView -->
    <TextView
        android:id="@+id/tv_count"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="0"
        android:textSize="60sp"
        android:textStyle="bold"
        android:textColor="#000000" />

    <!-- Count Incrementor Button -->
    <Button
        android:id="@+id/btn_count"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="COUNT"
        android:textSize="18sp"
        android:layout_marginTop="32dp" />

</LinearLayout>
```

---

### B. Java Code Segment (`MainActivity.java`)
```java
package com.example.counterapp;

import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    private int mCount = 0;
    private TextView mTvCount;
    private Button mBtnCount;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mTvCount = findViewById(R.id.tv_count);
        mBtnCount = findViewById(R.id.btn_count);

        // Click Event Handler to increment counter
        mBtnCount.setOnClickListener(v -> {
            mCount++;
            mTvCount.setText(String.valueOf(mCount));
        });
    }
}
```
