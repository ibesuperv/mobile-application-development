# Data Storage Options, Preferences & SharedPreferences

---

## SECTION 1: Overview of Android Data Storage Options (Exam Core 10-Mark Matrix)

Android provides 6 primary persistent data storage options. Choosing the correct storage medium depends on data privacy requirements, structure, access speed, and file size.

```
                              ANDROID PERSISTENT STORAGE
                                          |
          +-------------------------------+-------------------------------+
          |                               |                               |
          v                               v                               v
  SHARED PREFERENCES              INTERNAL STORAGE                EXTERNAL STORAGE
  (Key-Value Primitives)         (Private App Directory)         (Public/Shared Files)
          |                               |                               |
          v                               v                               v
  SQLITE DATABASE                CONTENT PROVIDERS                CLOUD / FIREBASE
  (Structured Relational)        (Inter-App Sharing)             (Remote Sync NoSQL)
```

| Data Storage Option | Privacy / Access Level | Data Format / Type | Deletion Behavior on App Uninstall | Best Use Cases |
| :--- | :--- | :--- | :--- | :--- |
| **Shared Preferences** | Private to the app. | Key-Value pairs (`XML` format) of primitive types (`int`, `boolean`, `String`). | Automatically deleted. | User settings, high scores, session tokens, app theme state. |
| **Internal Storage** | Strictly Private to app (`MODE_PRIVATE`). | Raw files (text, binary, cache). | Automatically deleted. | Sensitive app data, temporary cached files (`getCacheDir()`). |
| **External Storage** | Public / World-Readable (requires Manifest permission). | Media files, documents, photos. | Removed ONLY if saved in `getExternalFilesDir()`. | User-created photos, exported PDF documents, public downloads. |
| **SQLite Database** | Private to the app. | Structured relational SQL tables. | Automatically deleted. | Complex repeating data (e.g. contacts list, catalog items). |
| **Content Providers** | Configurable (Public or Private inter-app access). | Relational data abstraction. | Managed by hosting application. | Sharing app data with external apps (e.g., Contacts, MediaStore). |
| **Firebase Realtime DB** | Cloud Hosted / Synchronized. | JSON tree structure. | Persisted in Cloud. | Real-time multi-device sync, offline-first cloud databases. |

---

## SECTION 2: Internal vs. External Storage (Exam 6-Mark Comparison)

### 1. Key Differences Matrix:

| Feature Dimension | Internal Storage | External Storage |
| :--- | :--- | :--- |
| **Availability** | Always guaranteed to be available. | May be unmounted, missing (SD card), or connected to a PC via USB. |
| **Access Rights** | Restricted strictly to the owning app. | World-Readable. Any app with permissions can access. |
| **Permissions Needed** | **No permissions required**. | Requires `READ_EXTERNAL_STORAGE` or `WRITE_EXTERNAL_STORAGE`. |
| **Directory Methods** | `getFilesDir()`, `getCacheDir()`. | `getExternalFilesDir()`, `Environment.getExternalStoragePublicDirectory()`. |

---

### 2. Code Implementation: Internal vs. External Storage

```java
// A. Internal Storage File Output Stream
String filename = "user_notes.txt";
String string = "Exam Study Notes";
FileOutputStream outputStream;

try {
    // MODE_PRIVATE ensures file is private to this app
    outputStream = openFileOutput(filename, Context.MODE_PRIVATE);
    outputStream.write(string.getBytes());
    outputStream.close();
} catch (Exception e) {
    e.printStackTrace();
}

// B. External Storage Availability Check (Mandatory before reading/writing)
public boolean isExternalStorageWritable() {
    String state = Environment.getExternalStorageState();
    return Environment.MEDIA_MOUNTED.equals(state);
}
```

---

## SECTION 3: SharedPreferences (Exam Core Topic)

**`SharedPreferences`** is a lightweight mechanism to read and write key-value pairs of primitive data types (`boolean`, `float`, `int`, `long`, `String`, `Set<String>`) persistently to an underlying XML file on device storage.

---

### 1. `SharedPreferences` vs. `SavedInstanceState` (Exam 4-Mark Distinction)

| Dimension | `SharedPreferences` | `SavedInstanceState` (`Bundle`) |
| :--- | :--- | :--- |
| **Persistence Horizon** | **Permanent across user sessions**. Data survives app restarts, task kills, and device reboots. | **Transient across Activity instances**. Data survives rotation within the SAME user session only. |
| **Primary Use Case** | User settings, volume preferences, login tokens, high score history. | Preserving active UI state during rotation (e.g. scroll position, unsaved input text). |
| **Storage Backing** | Written asynchronously to an XML file on disk. | Held in system RAM memory bundle. |

---

### 2. `SharedPreferences` Lifecycle & Operations

```
                    SHAREDPREFERENCES OPERATIONAL FLOW
                                    |
   +--------------------------------+--------------------------------+
   |                                                                 |
   v                                                                 v
READ / RESTORE                                            WRITE / SAVE
(Inside onCreate())                                       (Inside onPause())
   |                                                                 |
mPreferences.getInt("key", defaultVal);             SharedPreferences.Editor editor = mPreferences.edit();
                                                    editor.putInt("key", value);
                                                    editor.apply(); // Asynchronous Write
```

---

### 3. `apply()` vs. `commit()` (Exam 4-Mark Question)

| Method | Execution Mode | Return Value | Main Thread Impact | Recommendation |
| :--- | :--- | :--- | :--- | :--- |
| **`apply()`** | **Asynchronous** (Writes to in-memory cache instantly, commits to disk off UI thread). | `void` | **Non-blocking** (Safe for UI thread). | **PREFERRED / RECOMMENDED**. |
| **`commit()`** | **Synchronous** (Writes directly to disk storage synchronously). | `boolean` (Returns success/failure). | **Blocking** (Can freeze UI thread / cause ANRs). | Discouraged unless return status is strictly needed. |

---

### 4. Complete Executable Code Pattern: `SharedPreferences`

```java
public class MainActivity extends AppCompatActivity {

    private SharedPreferences mPreferences;
    private String sharedPrefFile = "com.example.android.hellosharedprefs";
    private int mCount = 0;
    private TextView mShowCountTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mShowCountTextView = (TextView) findViewById(R.id.textview_count);

        // 1. Obtain SharedPreferences handle in MODE_PRIVATE
        mPreferences = getSharedPreferences(sharedPrefFile, MODE_PRIVATE);

        // 2. Restore preferences in onCreate()
        mCount = mPreferences.getInt("count_key", 0); // 0 is the default value
        mShowCountTextView.setText(String.valueOf(mCount));
    }

    // 3. Save preferences in onPause()
    @Override
    protected void onPause() {
        super.onPause();

        SharedPreferences.Editor preferencesEditor = mPreferences.edit();
        preferencesEditor.putInt("count_key", mCount);
        
        // Asynchronously save changes to disk
        preferencesEditor.apply();
    }

    // 4. Clear preferences
    public void resetPreferences(View view) {
        SharedPreferences.Editor preferencesEditor = mPreferences.edit();
        preferencesEditor.clear(); // Clears all key-value pairs
        preferencesEditor.apply();

        mCount = 0;
        mShowCountTextView.setText(String.valueOf(mCount));
    }
}
```

---

### 5. Preference Change Listener (`OnSharedPreferenceChangeListener`)

Listens for preference changes (e.g. inside a `SettingsActivity`):

```java
public class SettingsActivity extends AppCompatActivity 
        implements SharedPreferences.OnSharedPreferenceChangeListener {

    private SharedPreferences mPreferences;

    @Override
    protected void onResume() {
        super.onResume();
        // Register change listener in onResume()
        mPreferences.registerOnSharedPreferenceChangeListener(this);
    }

    @Override
    protected void onPause() {
        super.onPause();
        // Unregister change listener in onPause() to prevent memory leaks
        mPreferences.unregisterOnSharedPreferenceChangeListener(this);
    }

    @Override
    public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
        if (key.equals("sync_frequency_key")) {
            // React immediately to updated setting
        }
    }
}
```
