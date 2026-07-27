# Question Paper Solutions: Part-A (2-Mark Short Answers)

---

### 1.1 List and compare the features of Android OS with other OS. (2 Marks)
- **Features**: Open-source (AOSP), multi-vendor hardware ecosystem, customizable UI/widgets, unified inter-app data sharing via Content Providers.
- **Comparison**: Unlike iOS (closed source, single-vendor Apple hardware) or Windows Phone, Android allows complete source code customization, side-loading apps, and runs across thousands of device types.

---

### 1.2 What is APK in Android? List its components. (2 Marks)
- **Definition**: **APK (Android Package)** is the compiled ZIP archive format (`.apk`) used by Android to distribute and install applications.
- **Components**: `classes.dex` (compiled bytecode), `AndroidManifest.xml`, `res/`, `resources.arsc`, `META-INF/` (digital signature), `assets/`, `lib/` (native C/C++ libraries).

---

### 1.3 How is DVM useful than JVM in Android? (2 Marks)
- **Register-based Architecture**: Dalvik Virtual Machine (DVM) uses registers rather than JVM's memory stack, requiring fewer CPU instructions.
- **Memory Footprint**: Designed specifically for low-RAM mobile hardware; packages multiple `.class` files into a single compressed `classes.dex` file.

---

### 1.4 Write the flowchart of Activity life cycle. (2 Marks)
```
  [Non-Existent] ──> onCreate() ──> onStart() ──> onResume() ──> [Running]
                                                                     │
  [Destroyed] <── onDestroy() <── onStop() <── onPause() <───────────┘
```

---

### 1.5 Give the definition of intents and show an example. (2 Marks)
- **Definition**: An **Intent** is an asynchronous messaging object used to request an action from another application component (Activity, Service, Broadcast Receiver).
- **Example**: 
  ```java
  Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("https://google.com"));
  startActivity(intent);
  ```

---

### 1.6 Show the advantages of RecyclerView. (2 Marks)
1. **View Recycling**: Recycles `ViewHolder` objects so views aren't constantly recreated during scrolling, saving memory and preventing frame lag.
2. **Flexible Layouts**: Decouples item layout via `LayoutManager` (Linear, Grid, StaggeredGrid) and animations via `ItemAnimator`.

---

### 1.7 What is importance of services in Android? Explain. (2 Marks)
- **Importance**: Allows apps to perform long-running background tasks (such as music streaming, file downloading, or location tracking) without presenting a UI and without being killed when the user switches apps.

---

### 1.8 List the steps of connecting Android app to SQLite. (2 Marks)
1. Extend **`SQLiteOpenHelper`** and implement `onCreate()` (executing `CREATE TABLE`) and `onUpgrade()`.
2. Obtain a database handle (`getWritableDatabase()` / `getReadableDatabase()`).
3. Execute CRUD queries using **`SQLiteDatabase`** methods (`insert()`, `query()`, `update()`, `delete()`).

---

### 1.9 Differentiate between Android app and Web page application. (2 Marks)
| Dimension | Native Android App | Web Page Application |
| :--- | :--- | :--- |
| **Execution** | Runs natively on device OS using ART/DVM. | Runs inside a browser engine using HTML/CSS/JS. |
| **Hardware Access** | Direct access to hardware (Sensors, Camera, Bluetooth). | Restricted browser sandbox access. |
| **Offline Support** | Fully functional offline with local SQLite storage. | Requires continuous network connectivity (unless PWA). |

---

### 1.10 Show the connectivity between Firebase and AdMob. (2 Marks)
- **Integration**: AdMob connects directly to Firebase by linking the AdMob App ID inside the Firebase Console.
- **Analytics & Revenue**: Firebase Analytics tracks user engagement, session duration, and user demographics, which AdMob uses to serve highly targeted banner/interstitial ads and measure ad revenue per user segment.
