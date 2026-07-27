# Question Paper Solutions: Unit-2 Core Topics

---

## 5. Briefly Explain Attributes of Content Providers in Android with an Example (10 Marks)

### A. Core Concept & Definition (2 Marks)
A **`ContentProvider`** is a fundamental Android component that manages access to a structured repository of data. It encapsulates data and provides a unified, secure interface for sharing data across different applications without exposing the underlying storage implementation (SQLite database, flat files, or cloud storage).

---

### B. Core Attributes of Content Providers (5 Marks)

1. **`android:authorities` (Mandatory)**:
   - Defines a globally unique URI authority string identifying the provider.
   - Used by `ContentResolver` to locate the provider (e.g. `android:authorities="com.example.provider.wordlist"`).
2. **`android:exported`**:
   - `true`: Accessible to external applications on the device.
   - `false`: Accessible ONLY within the hosting application.
3. **`android:readPermission` & `android:writePermission`**:
   - Restricts read/write access to external client applications that hold specific declared permissions.
4. **`android:grantUriPermissions`**:
   - Allows temporary, fine-grained access permissions to specific data URIs (e.g., sharing a single file attachment via an Intent).
5. **`android:name`**:
   - The fully qualified class name of the `ContentProvider` subclass (e.g. `.MyContentProvider`).

---

### C. Executable Example (3 Marks)

#### 1. Manifest Declaration (`AndroidManifest.xml`):
```xml
<provider
    android:name=".WordContentProvider"
    android:authorities="com.example.wordapp.provider"
    android:exported="true"
    android:readPermission="com.example.wordapp.READ_WORDS"
    android:writePermission="com.example.wordapp.WRITE_WORDS" />
```

#### 2. Querying ContentProvider via `ContentResolver` (`MainActivity.java`):
```java
// Content URI: content://com.example.wordapp.provider/words
Uri wordsUri = Uri.parse("content://com.example.wordapp.provider/words");

Cursor cursor = getContentResolver().query(
        wordsUri,
        new String[]{"_id", "word"}, // Projection
        null, null, null              // Selection, args, sort order
);

if (cursor != null && cursor.moveToFirst()) {
    do {
        String word = cursor.getString(cursor.getColumnIndexOrThrow("word"));
        Log.d("ContentProvider", "Word: " + word);
    } while (cursor.moveToNext());
    cursor.close();
}
```

---

## 6. Briefly Explain Attributes of Intents in Android with an Example (10 Marks)

### A. Core Concept & Definition (2 Marks)
An **`Intent`** is an asynchronous messaging object used to request an action from another application component (Activity, Service, or BroadcastReceiver). It serves as the primary glue for navigation and inter-component communication in Android.

---

### B. Core Attributes of Intents (5 Marks)

1. **Component Name (`setComponent()`)**:
   - Specifies the explicit target component class name. If provided, creates an **Explicit Intent**; if omitted, creates an **Implicit Intent**.
2. **Action (`setAction()`)**:
   - A string specifying the generic action to perform (e.g. `ACTION_VIEW`, `ACTION_DIAL`, `ACTION_SEND`).
3. **Data & Type (`setData()`, `setType()`, `setDataAndType()`)**:
   - **Data**: URI referencing data to be acted upon (e.g. `tel:9876543210`, `content://contacts/people/1`).
   - **Type**: MIME type of the target data (e.g. `image/png`, `text/plain`).
4. **Category (`addCategory()`)**:
   - Gives additional context about the target component (e.g. `CATEGORY_LAUNCHER`, `CATEGORY_BROWSABLE`).
5. **Extras (`putExtra()`, `getExtras()`)**:
   - Key-value pairs (`Bundle`) carrying additional information required to execute the action.
6. **Flags (`setFlags()`)**:
   - Instructs the Android OS on how to launch the Activity and manage tasks (e.g. `FLAG_ACTIVITY_NEW_TASK`, `FLAG_ACTIVITY_CLEAR_TOP`).

---

### C. Executable Example: Explicit vs. Implicit Intents (3 Marks)

#### 1. Explicit Intent (Targeting internal Activity):
```java
Intent explicitIntent = new Intent(this, SecondActivity.class);
explicitIntent.putExtra("user_name", "Alice"); // Extra attribute
startActivity(explicitIntent);
```

#### 2. Implicit Intent (Targeting external Web Browser):
```java
Intent implicitIntent = new Intent(Intent.ACTION_VIEW); // Action attribute
implicitIntent.setData(Uri.parse("https://developer.android.com")); // Data URI attribute

// Defensive check before launching
if (implicitIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(implicitIntent);
}
```

---

## 7. Briefly Explain Attributes of Services in Android with an Example (10 Marks)

### A. Core Concept & Definition (2 Marks)
A **`Service`** is an application component that performs long-running background operations without providing a user interface. It runs on the **Main Thread** by default unless worker threads are explicitly spawned.

---

### B. Core Attributes of Services (5 Marks)

1. **Service Type (Started vs. Bound vs. Foreground)**:
   - **Started (`startService()`)**: Operates indefinitely in background until `stopSelf()` / `stopService()`.
   - **Bound (`bindService()`)**: Offers a client-server `IBinder` interface for Inter-Process Communication (IPC).
   - **Foreground (`startForeground()`)**: Highly visible; requires an ongoing status bar notification.
2. **`android:exported`**:
   - Controls whether external apps can trigger or bind to the service.
3. **`android:permission`**:
   - Enforces security permissions required by other apps to interact with the service.
4. **`onStartCommand()` Return Flags**:
   - `START_STICKY`: System recreates service if killed, passing `null` intent.
   - `START_NOT_STICKY`: System does NOT recreate service unless pending intents exist.
   - `START_REDELIVER_INTENT`: System recreates service and redelivers last intent.

---

### C. Executable Example: Started Service (`MyBackgroundService.java`) (3 Marks)

```java
public class MyBackgroundService extends Service {

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        // Offload long-running task to a background worker thread
        new Thread(() -> {
            // Background file download or processing logic
            stopSelf(); // Stop service when finished
        }).start();

        return START_STICKY; // Service restart attribute
    }

    @Override
    public IBinder onBind(Intent intent) {
        return null; // Not a bound service
    }
}
```

---

## 8. Briefly Explain Attributes of Broadcast Receivers in Android with an Example (10 Marks)

### A. Core Concept & Definition (2 Marks)
A **`BroadcastReceiver`** is a component that enables an app to listen for and respond to system-wide or app-specific broadcast messages (Intents), such as device boot completed, low battery, or network change announcements.

---

### B. Core Attributes of Broadcast Receivers (5 Marks)

1. **Registration Mode (Static vs. Dynamic)**:
   - **Static (`<receiver>` in Manifest)**: System wakes app up to receive broadcast even if app is closed.
   - **Dynamic (`registerReceiver()` in Code)**: Tied to component lifecycle (`onResume()`/`onPause()`).
2. **Intent Filters (`<intent-filter>`)**:
   - Specifies exact broadcast actions the receiver listens for (e.g. `ACTION_BOOT_COMPLETED`, `ACTION_POWER_CONNECTED`).
3. **`android:exported`**:
   - Controls whether the receiver can receive broadcasts from external non-system apps.
4. **`android:permission`**:
   - Restricts which senders can deliver broadcasts to this receiver.
5. **Execution Limit**:
   - `onReceive()` executes on the **Main Thread** and must complete within **10 seconds** to avoid ANR.

---

### C. Executable Example: Dynamic Broadcast Receiver (`PowerReceiver.java`) (3 Marks)

```java
public class MainActivity extends AppCompatActivity {

    private BroadcastReceiver mPowerReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            String action = intent.getAction();
            if (Intent.ACTION_POWER_CONNECTED.equals(action)) {
                Toast.makeText(context, "Charger Connected!", Toast.LENGTH_SHORT).show();
            }
        }
    };

    @Override
    protected void onResume() {
        super.onResume();
        // Register receiver dynamically for power connection events
        IntentFilter filter = new IntentFilter(Intent.ACTION_POWER_CONNECTED);
        registerReceiver(mPowerReceiver, filter);
    }

    @Override
    protected void onPause() {
        super.onPause();
        // Unregister receiver to prevent memory leaks
        unregisterReceiver(mPowerReceiver);
    }
}
```
