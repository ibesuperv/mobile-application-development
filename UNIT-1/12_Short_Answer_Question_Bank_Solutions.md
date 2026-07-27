# Short Answer & 2-Mark Question Bank Solutions

---

### 1. How does Gargenta approach the problem of the app acting sluggishly due to network latency?
**Answer**: Offloading network requests to **background threads** (using `AsyncTask`, worker threads, or background services) so the main UI thread remains responsive and unblocked.

---

### 2. What are the functionalities of HTTP Client interface in Android?
**Answer**: Opens HTTP/HTTPS connections, manages request/response headers, transmits GET/POST data streams, handles status codes (e.g. 200 OK, 404 Not Found), and reads server responses.

---

### 3. What is the purpose of `super.onCreate()` in Android?
**Answer**: Executes the base `Activity` class initialization logic (restoring saved instance state bundles, setting up system window framing, and lifecycle hooks) before app-specific UI setup.

---

### 4. What is an Android View Group (`ViewGroup`)?
**Answer**: An invisible container layout class extending `View` that holds and arranges child Views and manages their screen geometry (e.g., `LinearLayout`, `RelativeLayout`).

---

### 5. What is a Pending Intent in Android?
**Answer**: A token wrapper around an `Intent` that grants foreign applications or system services (like `NotificationManager` or `AlarmManager`) permission to execute the contained Intent with your app's identity/permissions at a later time.

---

### 6. In Which Directory Are XML Layout Files Stored?
**Answer**: `res/layout/`

---

### 7. If your service is private to your own application and runs in the same process as the client, you should create your interface by extending the ________ class?
**Answer**: **`Binder`** (or `LocalBinder` extending `Binder`).

---

### 8. Definition of Loader?
**Answer**: An Android framework component (`AsyncTaskLoader` / `CursorLoader`) that asynchronously loads data in the background and delivers fresh results to an Activity/Fragment while surviving configuration changes like screen rotation.

---

### 9. What are the direct/indirect subclasses of Services?
**Answer**: 
- **Direct Subclasses**: `IntentService`, `JobService`, `MediaBrowserService`, `TileService`, `AccessibilityService`, `VpnService`.
- **Indirect Subclasses**: Custom user-defined service implementations extending `IntentService` or `JobService`.

---

### 10. What is the difference between Activity Context and Application Context?
**Answer**: 
- **Activity Context**: Tied to the lifespan of a single Activity; used for UI inflation, dialogs, and toast popups.
- **Application Context**: Tied to the global lifecycle of the entire application process; used for singleton initialization, database helper instantiation, and long-running background tasks.

---

### 11. When would a ContentProvider be activated?
**Answer**: Activated when an application calls CRUD methods (`query()`, `insert()`, `update()`, `delete()`) via a **`ContentResolver`** matching the `ContentProvider`'s URI authority.

---

### 12. When the activity is not in focus, but still visible on the screen, it is in?
**Answer**: **Paused State** (or `onPause()` state; e.g., partially covered by a transparent dialog or multi-window screen).

---

### 13. Characteristics of Loaders?
**Answer**: 
1. Run on background threads off the main UI thread.
2. Monitor data sources and automatically deliver new query results when data changes.
3. Automatically retain their cached data across Activity configuration changes (screen rotations).

---

### 14. What are the direct/indirect subclasses of Activity?
**Answer**: 
- **Direct Subclasses**: `ComponentActivity`, `FragmentActivity`, `AccountAuthenticatorActivity`, `ExpandableListActivity`.
- **Indirect Subclasses**: `AppCompatActivity`, `PreferenceActivity`, `ListActivity`.

---

### 15. What is `AndroidManifest.xml` in Android?
**Answer**: A mandatory XML configuration file located at the project root that declares application metadata, components (`Activity`, `Service`, `Receiver`, `Provider`), required permissions, hardware requirements, and launcher entry points to the Android OS.

---

### 16. Which features are considered while creating an Android application?
**Answer**: Screen size/density adaptability, battery efficiency, target API level compatibility, background thread offloading, security/permissions model, and Material Design UI responsiveness.

---

### 17. How many broadcast receivers are available in Android?
**Answer**: **Two types** of registration:
1. **Static Broadcast Receivers** (Declared in `AndroidManifest.xml`).
2. **Dynamic Broadcast Receivers** (Registered programmatically in code using `registerReceiver()`).

---

### 18. Which Media Format Is Not Supported By Android out-of-the-box?
**Answer**: **Apple ProRes**, **WMV (Windows Media Video - uncompressed native)**, or **RMVB (RealMedia Variable Bitrate)** (Standard supported formats are MP3, AAC, MP4, H.264, WebM, Ogg).

---

### 19. Which one is NOT related to the Fragment class?
**Answer**: **`setContentView()`** (This is an `Activity` method; Fragments use `onCreateView()` to inflate layouts).

---

### 20. What was the main reason for replacing the Java VM with the Dalvik VM when the project began?
**Answer**: Dalvik VM was designed specifically for mobile devices to **minimize memory footprint (RAM)** and **optimize CPU/battery usage** using a register-based architecture rather than a stack-based Java VM.

---

### 21. What is the name of the program that converts Java byte code into Dalvik byte code?
**Answer**: **`dx` compiler** (or modern **`D8` / `R8` DEX compiler**).

---

### 22. The Emulator is identical to running a real phone EXCEPT when emulating/simulating what?
**Answer**: Real hardware sensors (accelerometer/gyroscope), actual mobile network carrier calls/SMS signaling, physical camera hardware behavior, and exact hardware battery thermal drain.

---

### 23. The XML file that contains all the text that your application uses.
**Answer**: **`res/values/strings.xml`**

---

### 24. You can shut down an activity by calling its _______ method.
**Answer**: **`finish()`** (or `finishAndRemoveTask()`).

---

### 25. What is an interface in Android?
**Answer**: A Java/Kotlin contract mechanism (or system component interface like `IBinder` / AIDL) that defines abstract callback methods implemented by classes to handle events or perform Inter-Process Communication (IPC).

---

### 26. When would a ContentProvider be activated?
**Answer**: Triggered when a client sends a request to its content URI via `ContentResolver` (e.g. `getContentResolver().query(uri, ...)`).

---

### 27. Once installed on a device, each Android application lives in _______?
**Answer**: Its own **Linux Security Sandbox** (a distinct Linux process with its own unique Linux User ID - UID).

---

### 28. If you need your interface to work across different processes, you can create an interface for the service with a ________?
**Answer**: **AIDL** (Android Interface Definition Language).

---

### 29. Which component is NOT activated by an Intent?
**Answer**: **`ContentProvider`** (Activated via `ContentResolver`; Activities, Services, and Broadcast Receivers are activated by Intents).

---

### 30. How is a simulator different from an emulator?
**Answer**: 
- **Simulator**: Simulates software behavior and environment without mimicking target hardware architecture (e.g. iOS Simulator).
- **Emulator**: Emulates both target hardware architecture (CPU, memory, storage) and software OS (e.g. Android QEMU Emulator).

---

### 31. Android tries hard to _____ low-level components, such as the software stack, with interfaces so that vendor-specific code can be managed easily.
**Answer**: **Abstract** (via the **Hardware Abstraction Layer - HAL**).

---

### 32. If you want to share data across all applications, you should go for?
**Answer**: **ContentProvider**

---

### 33. Dialog classes in Android?
**Answer**: `AlertDialog`, `DialogFragment`, `DatePickerDialog`, `TimePickerDialog`, `ProgressDialog` (Deprecated).

---

### 34. Which is NOT a drawable resource?
**Answer**: **Java `.class` file** or **Raw `Layout XML`** (Valid drawables: Bitmap images PNG/JPEG, NinePatch images, Vector Drawables, Shape Drawables, LayerList).

---

### 35. Attributes of manifest file `<data>` tag?
**Answer**: `android:scheme`, `android:host`, `android:port`, `android:path`, `android:mimeType`.

---

### 36. Which one of the following is NOT a manifest permission?
**Answer**: Any custom string not in system permissions (e.g., `android.permission.MAKE_MONEY` or layout attributes like `android:layout_width`).

---

### 37. Automate building, testing, deployment of Android code is done by?
**Answer**: **Gradle** (along with CI/CD tools like Jenkins, GitHub Actions, or Fastlane).

---

### 38. Difference between Android API and Google API?
**Answer**: 
- **Android API**: Open-source core framework APIs included in AOSP (e.g., Activity, Service, View, SQLite).
- **Google API**: Proprietary Google Play Services APIs (e.g., Google Maps, Google Sign-In, Firebase, AdMob).

---

### 39. Parent class of Service?
**Answer**: **`android.app.Service`** (Extends `ContextWrapper` $\rightarrow$ `Context`).

---

### 40. Layouts in Android?
**Answer**: `LinearLayout`, `RelativeLayout`, `ConstraintLayout`, `FrameLayout`, `TableLayout`, `GridLayout`, `AbsoluteLayout` (Deprecated).

---

### 41. What are the Direct subclasses of Activity?
**Answer**: `ComponentActivity`, `FragmentActivity`, `AccountAuthenticatorActivity`, `ExpandableListActivity`.

---

### 42. How many ways to start services?
**Answer**: **Two ways**:
1. **Started Service**: `startService()` or `startForegroundService()`.
2. **Bound Service**: `bindService()`.

---

### 43. Parent class of Activity?
**Answer**: **`android.app.Activity`** (Extends `ContextThemeWrapper` $\rightarrow$ `ContextWrapper` $\rightarrow$ `Context`).

---

### 44. In which technique can we refresh dynamic content in Android?
**Answer**: **SwipeRefreshLayout** (or using `RecyclerView.Adapter.notifyDataSetChanged()`, `LiveData` observers, or `Loaders`).

---

### 45. Action Bar Can Be Associated To?
**Answer**: **Activity** (or `FragmentActivity` / `AppCompatActivity`).

---

### 46. Which Level Of Security exists In Android?
**Answer**: 
1. **Linux Kernel Sandbox Level** (Process UID isolation).
2. **Framework Permission Level** (Normal vs Dangerous Runtime Permissions).

---

### 47. Which Code Used By Android Is NOT Open Source?
**Answer**: **Google Play Services** / **Proprietary Google Apps (GApps)** binaries (Core Android/AOSP code is open source).

---

### 48. Creating a UI (User Interface) in Android requires careful use of…
**Answer**: **Views** and **ViewGroups** (Layout Managers & XML View hierarchy).

---

### 49. What is the driving force behind an Android application and that ultimately gets converted into a Dalvik executable?
**Answer**: **Java / Kotlin Source Code** (compiled into `.class` files by `javac`, then converted into `classes.dex` by the `dx` / `D8` compiler).

---

### 50. The emulated device for Android?
**Answer**: **AVD (Android Virtual Device)** running on the Android Emulator (QEMU).

---

### 51. When an activity doesn’t exist in memory, it is in?
**Answer**: **Non-existent / Destroyed State** (Not created yet or after `onDestroy()`).

---

### 52. Intents?
**Answer**: Asynchronous messaging objects used to request actions from other components (Activities, Services, Broadcast Receivers).

---

### 53. Which of the following can be accomplished by using the `PhoneNumberUtils` class?
**Answer**: Formatting phone numbers according to national/international standards, normalizing phone strings, comparing two phone numbers for equality, and validating phone number characters.

---

### 54. Which of the following are valid features that you can request using `requestWindowFeature`?
**Answer**: `FEATURE_NO_TITLE`, `FEATURE_ACTION_BAR`, `FEATURE_INDETERMINATE_PROGRESS`, `FEATURE_CONTEXT_MENU`.
