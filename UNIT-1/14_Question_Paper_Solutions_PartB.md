# Question Paper Solutions: Part-B (Long Answer Solutions)

---

## Question 2a: Android Platform Challenges (10 Marks)

While Android provides rich functionality, developers face several technical challenges:

```
                            ANDROID DEVELOPMENT CHALLENGES
                                          |
          +-------------------------------+-------------------------------+
          |                               |                               |
          v                               v                               v
HARDWARE & OS FRAGMENTATION        BATTERY & RESOURCE LIMITS       SECURITY & PRIVACY RISKS
(Thousands of device sizes,        (Background limits, RAM,        (Malware, reverse engineering,
 resolutions, API levels)          Doze mode, 16ms render limit)    dangerous permissions)
```

1. **Hardware & Device Fragmentation**:
   - Thousands of unique Android device models from different manufacturers (Samsung, Xiaomi, OnePlus).
   - Variations in screen sizes, resolutions, aspect ratios, processor architectures (ARM, x86), and sensors.
2. **OS & API Version Fragmentation**:
   - Devices run different Android OS versions (from Android 8 to Android 14).
   - Require AndroidX support libraries and runtime API level checks (`Build.VERSION.SDK_INT`).
3. **Battery & Power Management Constraints**:
   - OS background limits, Doze mode, and App Standby restrict background execution to conserve battery.
4. **Memory & Performance Management**:
   - Devices have constrained RAM. Operations on the Main Thread must render under **16ms per frame** to avoid screen stutter ("jank") or ANR (Application Not Responding) errors.
5. **Security & Reverse Engineering**:
   - Open nature of APK archives makes them vulnerable to decompilation; requires ProGuard/R8 obfuscation and runtime permission checks.

---

## Question 2b: Activity States and Lifecycle Callback Methods (6 Marks)

### Activity Lifecycle Diagram
```
                           [ Activity Started ]
                                    │
                                onCreate()
                                    │
                                 onStart()
                                    │
                                onResume()
                                    │
                            [ Resumed / Running ]
                             (Has User Focus)
                                    │
                                 onPause() ───> [ Paused State ] (Partially Obscured)
                                    │
                                 onStop()  ───> [ Stopped State ] (Completely Hidden)
                                    │
                               onDestroy()
                                    │
                            [ Destroyed / Dead ]
```

### Activity States & Callbacks Matrix:

| Activity State | Description | Corresponding Callback |
| :--- | :--- | :--- |
| **Created** | Activity instance initialized in memory. | `onCreate()` |
| **Visible** | Activity is visible on screen but not yet interactive. | `onStart()` |
| **Resumed / Running** | Activity is in the foreground and has **user focus**. | `onResume()` |
| **Paused** | Activity is partially obscured (e.g. covered by dialog popup). | `onPause()` |
| **Stopped** | Activity is completely hidden from view. | `onStop()` |
| **Destroyed** | Activity is removed from memory stack. | `onDestroy()` |

---

## Question 3a: Explain Briefly the Building Blocks of Android (6 Marks)

Android applications consist of **4 Core Building Blocks** declared in `AndroidManifest.xml`:

```
                           4 CORE BUILDING BLOCKS
                                      |
     +-----------------+--------------+--------------+-----------------+
     |                 |                             |                 |
     v                 v                             v                 v
 ACTIVITIES        SERVICES                  BROADCAST RECEIVERS   CONTENT PROVIDERS
 (UI Screens)      (Background Ops)          (System Listener)     (Data Sharing)
```

1. **Activities**:
   - Represents a single screen with a User Interface (`View`/`ViewGroup`). Entry point for user interaction.
2. **Services**:
   - Performs long-running background tasks without a UI (e.g., audio playback, file download).
3. **Broadcast Receivers**:
   - Listens for and responds to system-wide broadcast announcements (e.g., low battery, charger connected).
4. **Content Providers**:
   - Manages and securely shares a central app data repository with other applications using URIs.

---

## Question 3b / 5a: DatePicker & TimePicker App (Complete XML & Java) (10 Marks)

### Layout File (`activity_main.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="16dp">

    <TextView
        android:id="@+id/tv_selected_datetime"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Select Date &amp; Time"
        android:textSize="20sp"
        android:textStyle="bold" />

    <Button
        android:id="@+id/btn_pick_date"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="PICK DATE"
        android:layout_marginTop="20dp" />

    <Button
        android:id="@+id/btn_pick_time"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:layout_marginTop="12dp" />

</LinearLayout>
```

### Java Implementation (`MainActivity.java`)
```java
package com.example.datetimepickerapp;

import android.app.DatePickerDialog;
import android.app.TimePickerDialog;
import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;
import java.util.Calendar;

public class MainActivity extends AppCompatActivity {

    private TextView mTvDateTime;
    private Button mBtnDate, mBtnTime;
    private int mYear, mMonth, mDay, mHour, mMinute;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mTvDateTime = findViewById(R.id.tv_selected_datetime);
        mBtnDate = findViewById(R.id.btn_pick_date);
        mBtnTime = findViewById(R.id.btn_pick_time);

        mBtnDate.setOnClickListener(v -> showDatePicker());
        mBtnTime.setOnClickListener(v -> showTimePicker());
    }

    // DatePickerDialog Implementation
    private void showDatePicker() {
        final Calendar c = Calendar.getInstance();
        mYear = c.get(Calendar.YEAR);
        mMonth = c.get(Calendar.MONTH);
        mDay = c.get(Calendar.DAY_OF_MONTH);

        DatePickerDialog datePickerDialog = new DatePickerDialog(this,
                (view, year, monthOfYear, dayOfMonth) -> {
                    String selectedDate = dayOfMonth + "/" + (monthOfYear + 1) + "/" + year;
                    mTvDateTime.setText("Date: " + selectedDate);
                }, mYear, mMonth, mDay);
        datePickerDialog.show();
    }

    // TimePickerDialog Implementation
    private void showTimePicker() {
        final Calendar c = Calendar.getInstance();
        mHour = c.get(Calendar.HOUR_OF_DAY);
        mMinute = c.get(Calendar.MINUTE);

        TimePickerDialog timePickerDialog = new TimePickerDialog(this,
                (view, hourOfDay, minute) -> {
                    String selectedTime = String.format("%02d:%02d", hourOfDay, minute);
                    mTvDateTime.setText("Time: " + selectedTime);
                }, mHour, mMinute, true);
        timePickerDialog.show();
    }
}
```

---

## Question 4a: Illustrate Working of Toast in Android (5 Marks)

A **Toast** is a transient pop-up message displayed on screen for a short duration. It automatically fades out and does NOT accept user interaction events.

### Parameters & Methods:
- **`Toast.makeText(Context context, CharSequence text, int duration)`**: Static factory method to instantiate Toast.
  - `context`: Application/Activity context.
  - `text`: Message string to display.
  - `duration`: `Toast.LENGTH_SHORT` (~2s) or `Toast.LENGTH_LONG` (~3.5s).
- **`show()`**: Displays the Toast on screen.
- **`setGravity(int gravity, int xOffset, int yOffset)`**: Customizes Toast position.

```java
// Example Toast Usage
Toast toast = Toast.makeText(this, "Settings Saved!", Toast.LENGTH_SHORT);
toast.setGravity(Gravity.CENTER, 0, 0); // Custom positioning
toast.show();
```

---

## Question 4b: Logcat Usage to Write Log Messages (5 Marks)

**Logcat** is the Android logging utility that outputs system and application debug statements.

### Log Priority Levels:
1. **`Log.v(TAG, msg)`**: Verbose (Lowest priority).
2. **`Log.d(TAG, msg)`**: Debug (Development debugging).
3. **`Log.i(TAG, msg)`**: Info (General runtime information).
4. **`Log.w(TAG, msg)`**: Warning (Potential issue).
5. **`Log.e(TAG, msg)`**: Error (Unhandled exception/failure).

```java
public class MainActivity extends AppCompatActivity {
    private static final String TAG = "MyLogcatTag";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Log.d(TAG, "onCreate: Activity initialized successfully");
        try {
            int result = 10 / 0;
        } catch (ArithmeticException e) {
            Log.e(TAG, "Calculation error occurred", e);
        }
    }
}
```

---

## Question 4c: Steps to Show Ready-to-Use DatePicker Dialog (6 Marks)

### Steps to Implement DatePickerDialog:
1. Obtain current system date using `Calendar.getInstance()`.
2. Instantiate `DatePickerDialog(Context, OnDateSetListener, year, month, day)`.
3. Override `onDateSet(DatePicker view, int year, int month, int dayOfMonth)` listener callback.
4. Call `datePickerDialog.show()` to render the predefined UI dialog.

```java
// Ready-to-use DatePickerDialog steps
Calendar c = Calendar.getInstance();
DatePickerDialog dialog = new DatePickerDialog(this, 
    (view, year, month, day) -> {
        // Step 3: Extract selected date
        String date = day + "/" + (month + 1) + "/" + year;
    }, 
    c.get(Calendar.YEAR), c.get(Calendar.MONTH), c.get(Calendar.DAY_OF_MONTH)
);
dialog.show(); // Step 4: Display dialog
```

---

## Question 5b: Input Control (Switch) with Toast On/Off Response (5 Marks)

### Layout File Snippet (`activity_main.xml`)
```xml
<Switch
    android:id="@+id/switch_settings"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Enable Notifications"
    android:textSize="18sp" />
```

### Java Code Segment (`MainActivity.java`)
```java
Switch switchSettings = findViewById(R.id.switch_settings);

// Set OnCheckedChangeListener on Switch
switchSettings.setOnCheckedChangeListener((buttonView, isChecked) -> {
    if (isChecked) {
        Toast.makeText(MainActivity.this, "ON", Toast.LENGTH_SHORT).show();
    } else {
        Toast.makeText(MainActivity.this, "OFF", Toast.LENGTH_SHORT).show();
    }
});
```
