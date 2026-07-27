# Permissions, Libraries, Performance and Security

---

## SECTION 1: Android Permissions Model (Exam Core 10-Mark Question)

Android isolates applications in a security sandbox. Apps must explicitly request permission before accessing device hardware, user data, or network resources.

```
                          ANDROID PERMISSIONS MODEL
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
NORMAL PERMISSIONS                                       DANGEROUS PERMISSIONS
(No privacy risk; e.g. INTERNET)                         (Privacy/Data risk; e.g. CAMERA, CONTACTS)
Auto-granted at install time                             Requires Runtime User Dialog (Android 6.0+)
```

---

### 1. Normal vs. Dangerous Permissions Matrix

| Permission Level | Privacy Impact | Examples | System Handling |
| :--- | :--- | :--- | :--- |
| **Normal Permission** | Low risk to user privacy or data integrity. | `INTERNET`, `ACCESS_NETWORK_STATE`, `SET_WALLPAPER`, `VIBRATE`. | **Auto-granted** by Android at installation time. Must still be declared in Manifest. |
| **Dangerous Permission** | High risk; accesses private user data or sensitive hardware. | `CAMERA`, `READ_CONTACTS`, `ACCESS_FINE_LOCATION`, `SEND_SMS`, `RECORD_AUDIO`. | **Requires explicit runtime approval** from user via a dialog prompt on API 23+ (Android 6.0+). |

---

### 2. Runtime Permissions Handling (Android 6.0 / API 23+ Modern Standard)

Prior to Android 6.0, permissions were granted all-or-nothing at install time. Starting with API 23, users can grant or revoke individual dangerous permissions at runtime.

#### Step 1: Declare in `AndroidManifest.xml`
```xml
<uses-permission android:name="android.permission.CAMERA" />
```

#### Step 2: Runtime Permission Workflow Implementation (`MainActivity.java`)
```java
public class MainActivity extends AppCompatActivity {

    private static final int CAMERA_PERMISSION_REQUEST_CODE = 101;

    public void openCameraWithPermissionCheck() {
        // 1. Check if permission is already granted using ContextCompat
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA)
                == PackageManager.PERMISSION_GRANTED) {
            // Permission already granted -> Launch Camera
            launchCamera();
        } else {
            // 2. Request permission dynamically at runtime
            ActivityCompat.requestPermissions(
                this,
                new String[]{ Manifest.permission.CAMERA },
                CAMERA_PERMISSION_REQUEST_CODE
            );
        }
    }

    // 3. Handle user response callback
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, 
                                           @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);

        if (requestCode == CAMERA_PERMISSION_REQUEST_CODE) {
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                Toast.makeText(this, "Camera Permission Granted!", Toast.LENGTH_SHORT).show();
                launchCamera();
            } else {
                Toast.makeText(this, "Camera Permission Denied!", Toast.LENGTH_SHORT).show();
            }
        }
    }

    private void launchCamera() {
        // Camera launching logic
    }
}
```

---

## SECTION 2: External Libraries & Dependencies

Android projects use **Gradle** as the build automation tool to pull third-party libraries and Google Support / AndroidX libraries.

### 1. Adding Dependencies in `build.gradle` (Module: app)
```groovy
dependencies {
    // AndroidX & UI Components
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.9.0'

    // Third-party Image Loading Library (Glide)
    implementation 'com.github.bumptech.glide:glide:4.15.1'
    
    // Third-party Networking Library (Retrofit)
    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
}
```

---

## SECTION 3: Performance Optimization (Exam 8-Mark Core Question)

Creating performant Android apps requires rendering screens within **16 milliseconds per frame** (60 FPS refresh rate). Skipping frames causes stuttering ("jank").

---

### 1. UI Performance & Layout Optimization
- **The 16ms Rule**: High-speed display hardware updates every 16ms. Main thread execution longer than 16ms drops frames.
- **Flattening View Hierarchies**: Avoid deep layout nesting (e.g. `LinearLayout` inside `LinearLayout`). Replace with flat `ConstraintLayout`.
- **Reducing Overdraw**: Overdraw happens when the app draws the same pixel multiple times in a single frame (e.g. overlapping views with solid backgrounds).
  - *Fix*: Remove unnecessary window/view background drawables (`android:background="@null"`).

---

### 2. Android Studio Profiling Tools

| Profiling Tool | Primary Function / Measurement |
| :--- | :--- |
| **CPU Profiler** | Measures CPU usage in real-time; identifies long-running operations clogging the UI thread. |
| **Memory Profiler** | Detects memory allocation spikes and **Memory Leaks** (unreleased references causing eventual OutOfMemory crashes). |
| **Network Profiler** | Monitors real-time payload bytes sent/received and timing of network requests. |
| **Profile GPU Rendering** | Displays colored vertical bars on-screen on physical devices to identify frames exceeding the 16ms threshold. |

---

## SECTION 4: Security Best Practices

Android applications operate under a shared-trust security framework. Developers must proactively safeguard user data.

---

### 1. Key Security Rules Matrix

```
                             ANDROID SECURITY RULES
                                       |
    +----------------------------------+----------------------------------+
    |                                  |                                  |
    v                                  v                                  v
SECURE NETWORK TRAFFIC             INPUT VALIDATION                  LOGGING SECURITY
(HTTPS mandatory; disable          (Parameterized SQL queries        (NEVER write private user
 cleartext HTTP)                    prevent SQL Injections)           data to Logcat logs)
```

1. **Secure Network Data Transmission**:
   - Always use **HTTPS** (`https://`) instead of cleartext HTTP (`http://`).
   - Use `SSLSocket` or TLS for custom encrypted socket communication.
2. **Preventing SQL Injections**:
   - Always use **Parameterized Queries** (`selectionArgs` in `ContentResolver` / `SQLiteDatabase`) instead of concatenating raw user strings into SQL queries.
3. **Logging Security**:
   - System logs (`Log.d()`, `Log.e()`) are accessible to apps holding `READ_LOGS`. **NEVER write sensitive user data (passwords, tokens, personal info) to Logcat!**
4. **WebView Security**:
   - Avoid enabling JavaScript (`setJavaScriptEnabled(true)`) on untrusted external web URLs to prevent Cross-Site Scripting (XSS) attacks.
