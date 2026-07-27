# Question Paper Solutions: Background Tasks & Security Paper (Part-B Questions 1 to 5)

---

## Question 1: Steps of Polishing and Publishing of the Android App (10 Marks)

Publishing an Android app involves 5 core phases:

```
                            APP PUBLISHING WORKFLOW
                                       │
 1. APP POLISHING: Remove debug logs (Log.d), optimize assets, update versionCode & versionName
                                       │
 2. BUILD SIGNED BUNDLE: Create Keystore (.jks) and generate signed Android App Bundle (.aab)
                                       │
 3. PLAY CONSOLE SETUP: Create Developer Account ($25 fee) & configure Store Listing metadata
                                       │
 4. RATING & POLICIES: Complete Content Rating Questionnaire & add mandatory Privacy Policy URL
                                       │
 5. RELEASE & REVIEW: Upload AAB to Production Track & submit for Google Play Store review
```

### 1. App Polishing Phase:
- **Remove Debug Statements**: Strip out `Log.d` / `Log.v` logging or set `minifyEnabled true` in `build.gradle`.
- **Configure Build Metadata**: Increment `versionCode` (integer) and update `versionName` (string) in `app/build.gradle`.
- **Resource Optimization**: Run R8/ProGuard to shrink, obfuscate, and optimize code.

### 2. Generate Signed Android App Bundle (AAB):
- Select **Build > Generate Signed Bundle / APK** in Android Studio.
- Create or select a secure **Keystore (`.jks`)** file specifying store password, key alias, and key password.
- Generate the signed **Android App Bundle (`.aab`)** for production.

### 3. Store Listing & Metadata Configuration:
- Create a Google Play Console Developer Account ($25 one-time registration fee).
- Fill out Store Listing details: App Title, Short Description, Full Description, App Icon (512x512), Feature Graphic (1024x500), and phone/tablet screenshots.

### 4. Content Rating & Privacy Policies:
- Complete the International Age Rating Coalition (IARC) content questionnaire.
- Provide a mandatory, publicly accessible **Privacy Policy URL**.

### 5. Production Rollout:
- Upload the signed `.aab` file to the Play Console **Production Track**.
- Submit the app for Google's automated safety scans and manual policy review.

---

## Question 2: Implementation of AsyncTaskLoader (Diagram, Subclass & Callbacks) (10 Marks)

### A. AsyncTaskLoader Architecture Block Diagram (3 Marks)

```
                            ASYNCTASKLOADER ARCHITECTURE
+-----------------------------------------------------------------------+
|  UI Thread (Activity / Fragment)                                      |
|                                                                       |
|  1. initLoader()  ────>  LoaderManager.LoaderCallbacks                |
|                                │                                      |
|                                v                                      |
|  3. onLoadFinished() <─── onCreateLoader() ──>  AsyncTaskLoader       |
+-------------------------------------------------------│---------------+
                                                        v  (Worker Thread)
                                                 2. loadInBackground()
                                                    (Fetches Data)
```

---

### B. Subclassing `AsyncTaskLoader` (3 Marks)
To build a loader, extend `AsyncTaskLoader<T>` and override **`loadInBackground()`** and **`onStartLoading()`**:

```java
public class WordLoader extends AsyncTaskLoader<List<String>> {

    public WordLoader(Context context) {
        super(context);
    }

    @Override
    protected void onStartLoading() {
        forceLoad(); // Mandatory to trigger loadInBackground()
    }

    @Override
    public List<String> loadInBackground() {
        // Heavy background operation (Database/Network fetch)
        List<String> words = new ArrayList<>();
        words.add("Android");
        words.add("AsyncTaskLoader");
        return words;
    }
}
```

---

### C. `LoaderManager.LoaderCallbacks<D>` Implementation (4 Marks)

```java
public class MainActivity extends AppCompatActivity 
        implements LoaderManager.LoaderCallbacks<List<String>> {

    @NonNull
    @Override
    public Loader<List<String>> onCreateLoader(int id, @Nullable Bundle args) {
        return new WordLoader(this); // Instantiates loader subclass
    }

    @Override
    public void onLoadFinished(@NonNull Loader<List<String>> loader, List<String> data) {
        // Updates UI with background loaded data
        mAdapter.setWords(data);
    }

    @Override
    public void onLoaderReset(@NonNull Loader<List<String>> loader) {
        // Clears references to stale data
        mAdapter.clear();
    }
}
```

---

## Question 3: Types of Permissions in Android (Grant/Revoke, Types & Best Practices) (10 Marks)

### A. Grant and Revoke Mechanisms (3 Marks)
- **Install-time Granting**: Normal permissions are declared in Manifest and automatically granted upon installation.
- **Runtime Granting**: On Android 6.0+ (API 23+), Dangerous permissions require explicitly prompting the user at runtime via `ActivityCompat.requestPermissions()`.
- **Revocation**: Users can manually revoke granted permissions at any time via **Settings > Apps > Permissions**.

---

### B. Types of Permissions (4 Marks)

1. **Normal Permissions**: Low risk to user privacy/data (e.g. `INTERNET`, `VIBRATE`, `ACCESS_NETWORK_STATE`). Automatically granted.
2. **Dangerous Permissions**: Accesses private user data or sensitive hardware (e.g. `CAMERA`, `READ_CONTACTS`, `ACCESS_FINE_LOCATION`, `SEND_SMS`). Requires runtime user dialog approval.
3. **Signature Permissions**: Granted automatically only if the requesting app is signed with the same digital certificate as the declaring application.
4. **Special Permissions**: Requires explicit system settings toggle (e.g. `SYSTEM_ALERT_WINDOW`, `WRITE_SETTINGS`).

---

### C. Best Practices for Permissions (3 Marks)

1. **Request Minimum Permissions**: Only ask for permissions essential to core functionality.
2. **Use Implicit Intents**: Use system intents (e.g. `ACTION_IMAGE_CAPTURE`) instead of asking for raw `CAMERA` hardware permissions.
3. **Provide Context Rationale**: Explain to the user why a permission is needed (`shouldShowRequestPermissionRationale`) before triggering the system dialog.

---

## Question 4: Steps to Implement a Broadcast Receiver (10 Marks)

Implementing a Broadcast Receiver requires 4 distinct steps:

```
                      BROADCAST RECEIVER IMPLEMENTATION
                                      │
  Step 1: Create BroadcastReceiver Subclass & Override onReceive()
                                      │
  Step 2: Choose Registration Method (Static vs Dynamic)
                                      │
  Step 3: Define Intent Filters (Listening for specific actions)
                                      │
  Step 4: Dispatch Broadcast via sendBroadcast()
```

### Step 1: Subclass `BroadcastReceiver` & Override `onReceive()`
```java
public class PowerReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (Intent.ACTION_POWER_CONNECTED.equals(intent.getAction())) {
            Toast.makeText(context, "Charger Plugged In!", Toast.LENGTH_SHORT).show();
        }
    }
}
```

---

### Step 2 & 3: Registration & Intent Filter Definition

#### Option A: Static Registration (`AndroidManifest.xml`):
```xml
<receiver 
    android:name=".PowerReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
    </intent-filter>
</receiver>
```

#### Option B: Dynamic Registration (`MainActivity.java`):
```java
private PowerReceiver mReceiver = new PowerReceiver();

@Override
protected void onResume() {
    super.onResume();
    IntentFilter filter = new IntentFilter(Intent.ACTION_POWER_CONNECTED);
    registerReceiver(mReceiver, filter); // Register
}

@Override
protected void onPause() {
    super.onPause();
    unregisterReceiver(mReceiver); // Mandatory unregister to avoid memory leaks
}
```

---

### Step 4: Dispatching Custom Broadcasts
```java
Intent customIntent = new Intent("com.example.CUSTOM_ACTION");
sendBroadcast(customIntent);
```

---

## Question 5: Security Practices to Incorporate During Android App Development (10 Marks)

```
                            ANDROID APP SECURITY PRACTICES
                                          │
    +-----------------+-------------------+------------------+-----------------+
    |                 |                   |                  |                 |
    v                 v                   v                  v                 v
SECURE DATA       HTTPS ENCRYPTION    R8 / PROGUARD      INTERNAL STORAGE    EXPLICIT INTENTS
STORAGE           (Network Security   OBFUSCATION        PRIVACY             & EXPORTED FALSE
(EncryptedPrefs)   Config)            (Anti-decompilation)(/data/data/pkg)   (Component isolation)
```

1. **Use EncryptedSharedPreferences & KeyStore**:
   - Store sensitive strings (auth tokens, passwords) using **`EncryptedSharedPreferences`** and master keys backed by the **Android KeyStore system**.
2. **Enforce HTTPS Network Security Config**:
   - Enforce HTTPS endpoints exclusively and disable cleartext HTTP traffic in `res/xml/network_security_config.xml`.
3. **Enable Code Obfuscation (R8 / ProGuard)**:
   - Set `minifyEnabled true` in `build.gradle` to shrink, obfuscate, and obscure DEX bytecode class names against reverse-engineering tools like `jadx`.
4. **Enforce Storage Privacy (`MODE_PRIVATE`)**:
   - Store application files exclusively in internal private memory (`/data/data/<pkg>/`) with `MODE_PRIVATE` access.
5. **Secure App Components (`android:exported="false"`)**:
   - Mark Activities, Services, and Receivers that are internal to the app as `android:exported="false"` in `AndroidManifest.xml` to prevent malicious external apps from launching them.
6. **SQL Injection Defense**:
   - Use parameterized SQL queries (`db.query()` with selection arguments `?`) instead of concatenating strings in `rawQuery()`.
