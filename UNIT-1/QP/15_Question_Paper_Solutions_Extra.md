# Question Paper Solutions: Additional Exam Paper (Questions 1 to 4)

---

## Question 1: Android Platform Challenges with Detailed Examples (10 Marks)

While Android provides rich functionality for application development, developers face 5 major engineering challenges:

```
                            ANDROID DEVELOPMENT CHALLENGES
                                          │
    +-----------------+-------------------+------------------+-----------------+
    |                 |                   |                  |                 |
    v                 v                   v                  v                 v
 HARDWARE           API VERSION         BATTERY & POWER    PERFORMANCE &       SECURITY &
 FRAGMENTATION      FRAGMENTATION       CONSTRAINTS        16MS FRAME LIMIT    REVERSE ENG.
 (Device sizes,     (API levels 21-34,  (Doze mode, App    (RAM bottlenecks,   (APK decompilation,
 screen densities)  support libraries)  Standby, alarms)   ANR >5s UI freeze)  runtime permissions)
```

1. **Hardware & Display Fragmentation**:
   - **Challenge**: Developing a UI that renders consistently across thousands of distinct device models with varying screen sizes (4" to 12"+), aspect ratios (16:9, 19.5:9), and display densities (`mdpi` to `xxxhdpi`).
   - **Example**: A fixed pixel `layout_width="300px"` button renders fine on a 720p screen but becomes tiny on a 4K display. *Solution*: Use density-independent pixels (`dp`) and resource qualifiers (`res/layout-sw600dp/`).
2. **OS & API Version Fragmentation**:
   - **Challenge**: Android devices run diverse OS versions ranging from Android 8.0 (API 26) to Android 14 (API 34).
   - **Example**: Notification Channels are mandatory on API 26+, runtime permissions on API 23+, and scoped storage on API 29+. *Solution*: Use AndroidX Support Libraries and `if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O)` checks.
3. **Battery & Power Consumption Constraints**:
   - **Challenge**: The Android OS aggressively manages background execution to preserve battery life.
   - **Example**: High-frequency GPS tracking or unbundled network requests drain battery fast. The OS enters **Doze Mode** when idle, deferring jobs. *Solution*: Use `JobScheduler` or `WorkManager` to batch background syncs.
4. **Performance & Memory Bottlenecks (16ms Render Limit & ANRs)**:
   - **Challenge**: Display hardware refreshes screens every **16 milliseconds** (60 FPS). Main-thread operations exceeding 16ms cause frame drops ("jank"). Blocking the main thread for $>5$ seconds triggers an **ANR (Application Not Responding)** dialog.
   - **Example**: Executing a SQLite query or network fetch on the main UI thread freezes the app. *Solution*: Offload work to background threads via `AsyncTaskLoader` or `Executors`.
5. **Security & Reverse Engineering Risks**:
   - **Challenge**: The open structure of `.apk` ZIP archives allows malicious actors to decompile DEX bytecode back to source code.
   - **Example**: Hardcoded API keys inside `MainActivity.java` can be extracted using `jadx`. *Solution*: Use R8/ProGuard code obfuscation and store keys securely.

---

## Question 2a: Importance of Mobile Applications & Why Develop for Android (5 Marks)

### A. Importance of Mobile Applications in Today's Scenario (2.5 Marks)
1. **Ubiquitous Accessibility**: Smartphones are personal, always-connected devices available 24/7 for banking, e-commerce, education, and healthcare.
2. **Context-Aware Experiences**: Mobile apps leverage hardware sensors (GPS, Camera, Accelerometer) to deliver location-based and real-time services.
3. **Instant Engagement**: Push notifications re-engage users instantly compared to traditional web portals.

---

### B. Reasons to Justify Developing Apps for Android (2.5 Marks)
1. **Dominant Global Market Share**: Android commands ~70%+ of the global smartphone operating system market share.
2. **Open Source & Low Entry Barrier**: Free Android Studio IDE, open-source AOSP platform, and a one-time $25 Google Play registration fee.
3. **Rich Feature Set & Google Services Integration**: Native access to Google Maps, Firebase, Machine Learning (ML Kit), and Google Sign-In.
4. **Flexible Distribution**: Multiple deployment channels (Google Play Store, Amazon Appstore, APK sideloading).

---

## Question 2b: Purpose of Intents & Categories with Case Study (5 Marks)

### Case Study Scenario: E-Commerce Shopping App
In a shopping app (`ShopApp`):
- Clicking "View Product Details" navigates from `ProductListActivity` to `ProductDetailActivity` inside the app (**Explicit Intent**).
- Clicking "Share Product" opens the user's preferred external app (WhatsApp, Email, SMS) to share the URL (**Implicit Intent**).

---

### Intent Categories & Examples:

```
                            CATEGORIES OF INTENTS
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
EXPLICIT INTENTS                                          IMPLICIT INTENTS
(Target component explicitly specified                   (Action & data specified; system
 by Class Name; used internally)                          finds matching external app)
```

1. **Explicit Intents**:
   - **Purpose**: Used to launch a specific application component within the *same application*.
   - **Example**:
     ```java
     Intent intent = new Intent(ProductListActivity.this, ProductDetailActivity.class);
     intent.putExtra("product_id", 101);
     startActivity(intent);
     ```
2. **Implicit Intents**:
   - **Purpose**: Used to request an action from *any external application* on the device that can handle the action.
   - **Example**:
     ```java
     Intent shareIntent = new Intent(Intent.ACTION_SEND);
     shareIntent.setType("text/plain");
     shareIntent.putExtra(Intent.EXTRA_TEXT, "Check out this product: https://shop.com/p101");
     startActivity(Intent.createChooser(shareIntent, "Share via"));
     ```

---

## Question 3a: Working of Toast in Android (Parameters & Methods) (5 Marks)

A **Toast** is a temporary pop-up notification displayed on top of the screen that does NOT interrupt user interaction and automatically disappears after a timeout.

```
                           TOAST ARCHITECTURE
                                   │
      Toast.makeText(context, "Saved!", Toast.LENGTH_SHORT)
                                   │
              setGravity(Gravity.CENTER, 0, 0)
                                   │
                                 show()
```

### Complete Specification of Parameters & Methods:

1. **`Toast.makeText(Context context, CharSequence text, int duration)`**:
   - **`context`**: Application or Activity `Context` instance.
   - **`text`**: The message string or string resource ID (`R.string.msg`) to be displayed.
   - **`duration`**: Display duration constant (`Toast.LENGTH_SHORT` ~2 seconds, or `Toast.LENGTH_LONG` ~3.5 seconds).
2. **`show()`**: Displays the Toast on screen.
3. **`setGravity(int gravity, int xOffset, int yOffset)`**: Customizes the screen anchor position (e.g. `Gravity.CENTER`, `Gravity.TOP`).
4. **`setText(CharSequence s)`**: Dynamically updates the Toast message text.

```java
// Complete Toast Code Example
Toast toast = Toast.makeText(getApplicationContext(), "File Uploaded Successfully", Toast.LENGTH_SHORT);
toast.setGravity(Gravity.CENTER, 0, 0); // Position in center of screen
toast.show();
```

---

## Question 3b: Log Class Usage & Role in Debugging (5 Marks)

The **`android.util.Log`** class writes debug log messages to **Logcat**, allowing developers to monitor runtime variable values, execution flow, and exceptions.

### Log Priority Levels & Code Segments:
```java
public class MainActivity extends AppCompatActivity {
    private static final String TAG = "AuthDebugTag";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 1. Verbose: Detailed tracing
        Log.v(TAG, "onCreate: Activity memory allocated");

        // 2. Debug: General development debugging
        Log.d(TAG, "Attempting login for user: john_doe");

        // 3. Info: Key operational milestones
        Log.i(TAG, "Network connection established");

        // 4. Warning: Potential issue, non-fatal
        Log.w(TAG, "Slow network response detected (>2000ms)");

        // 5. Error: Exception/Failure handling
        try {
            int result = 10 / 0;
        } catch (ArithmeticException e) {
            Log.e(TAG, "Division by zero exception in calculation", e);
        }
    }
}
```

### How Logcat Helps in Debugging:
- **Filtering by TAG**: Isolates logs generated by a specific class (`TAG = "AuthDebugTag"`).
- **Log Level Filtering**: Allows developers to view only `Error` or `Debug` messages.
- **StackTrace Extraction**: Captures the exact line number where an unhandled crash occurred.

---

## Question 3c: Steps to Show Ready-to-Use DatePickerDialog (5 Marks)

### Steps to Implement Predefined DatePickerDialog UI:
1. Obtain current date integers (Year, Month, Day) using `Calendar.getInstance()`.
2. Instantiate `DatePickerDialog(Context, OnDateSetListener, year, month, day)`.
3. Implement `OnDateSetListener.onDateSet(view, selectedYear, selectedMonth, selectedDay)` to process the user's selection.
4. Execute `datePickerDialog.show()` to present the native calendar UI dialog.

```java
// 1. Get current date
Calendar calendar = Calendar.getInstance();
int year = calendar.get(Calendar.YEAR);
int month = calendar.get(Calendar.MONTH);
int day = calendar.get(Calendar.DAY_OF_MONTH);

// 2 & 3. Create DatePickerDialog with Listener callback
DatePickerDialog datePickerDialog = new DatePickerDialog(this,
        (view, selectedYear, selectedMonth, selectedDay) -> {
            String dateString = selectedDay + "/" + (selectedMonth + 1) + "/" + selectedYear;
            Log.d("DateSelected", "Selected: " + dateString);
        }, year, month, day);

// 4. Display dialog
datePickerDialog.show();
```

---

## Question 4: Email App Login & Validation App (Complete XML, Java & Manifest) (10 Marks)

App containing:
- **Activity 1 (`MainActivity`)**: Displays intro screen with a **SIGNIN** button.
- **Activity 2 (`LoginActivity`)**: Displays Email/Username & Password fields with **validation** (NotEmpty check). Displays Toast `"Logged in"` on success, `"Cannot login"` on failure.

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

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Welcome to Email App"
        android:textSize="24sp"
        android:textStyle="bold" />

    <Button
        android:id="@+id/btn_signin"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="SIGNIN"
        android:layout_marginTop="24dp" />

</LinearLayout>
```

---

### B. Activity 1 Java Code (`MainActivity.java`)
```java
package com.example.emailapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button btnSignIn = findViewById(R.id.btn_signin);
        btnSignIn.setOnClickListener(v -> {
            // Open LoginActivity
            Intent intent = new Intent(MainActivity.this, LoginActivity.class);
            startActivity(intent);
        });
    }
}
```

---

### C. Activity 2 Login Layout (`activity_login.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="24dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Login Screen"
        android:textSize="22sp"
        android:textStyle="bold"
        android:layout_marginBottom="24dp" />

    <!-- Username / Email Field with email inputType validation -->
    <EditText
        android:id="@+id/et_username"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Username / Email"
        android:inputType="textEmailAddress" />

    <!-- Password Field with password inputType validation -->
    <EditText
        android:id="@+id/et_password"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Password"
        android:inputType="textPassword"
        android:layout_marginTop="12dp" />

    <!-- Login Button -->
    <Button
        android:id="@+id/btn_login"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="LOGIN"
        android:layout_marginTop="24dp" />

</LinearLayout>
```

---

### D. Activity 2 Java Code (`LoginActivity.java`)
```java
package com.example.emailapp;

import android.os.Bundle;
import android.text.TextUtils;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

public class LoginActivity extends AppCompatActivity {

    private EditText mEtUsername, mEtPassword;
    private Button mBtnLogin;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        mEtUsername = findViewById(R.id.et_username);
        mEtPassword = findViewById(R.id.et_password);
        mBtnLogin = findViewById(R.id.btn_login);

        mBtnLogin.setOnClickListener(v -> validateAndLogin());
    }

    private void validateAndLogin() {
        String username = mEtUsername.getText().toString().trim();
        String password = mEtPassword.getText().toString().trim();

        // 1. Validation using attributes and TextUtils
        if (TextUtils.isEmpty(username)) {
            mEtUsername.setError("Username cannot be empty");
            return;
        }

        if (TextUtils.isEmpty(password)) {
            mEtPassword.setError("Password cannot be empty");
            return;
        }

        // 2. Validate Credentials (Hardcoded test check: admin / 12345)
        if (username.equals("admin@gmail.com") && password.equals("12345")) {
            Toast.makeText(this, "Logged in", Toast.LENGTH_SHORT).show();
        } else {
            Toast.makeText(this, "Cannot login", Toast.LENGTH_SHORT).show();
        }
    }
}
```

---

### E. Manifest File (`AndroidManifest.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.emailapp">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="Email App"
        android:theme="@style/Theme.AppCompat.Light">

        <!-- Launcher Activity -->
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!-- Second Activity: LoginActivity -->
        <activity
            android:name=".LoginActivity"
            android:exported="false"
            android:parentActivityName=".MainActivity" />

    </application>
</manifest>
```
