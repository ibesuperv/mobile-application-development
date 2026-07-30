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

| Dimension           | Native Android App                                      | Web Page Application                                   |
| :------------------ | :------------------------------------------------------ | :----------------------------------------------------- |
| **Execution**       | Runs natively on device OS using ART/DVM.               | Runs inside a browser engine using HTML/CSS/JS.        |
| **Hardware Access** | Direct access to hardware (Sensors, Camera, Bluetooth). | Restricted browser sandbox access.                     |
| **Offline Support** | Fully functional offline with local SQLite storage.     | Requires continuous network connectivity (unless PWA). |

---

### 1.10 Show the connectivity between Firebase and AdMob. (2 Marks)

- **Integration**: AdMob connects directly to Firebase by linking the AdMob App ID inside the Firebase Console.
- **Analytics & Revenue**: Firebase Analytics tracks user engagement, session duration, and user demographics, which AdMob uses to serve highly targeted banner/interstitial ads and measure ad revenue per user segment.

# Question Paper Solutions: Part-A (Short Fill-in & 1-2 Mark Questions)

---

### 1. The Android SDK separates tools, platforms, and other components into packages which can be downloaded as needed using the ******\_******.

**Answer**: **SDK Manager**

---

### 2. Name the type of file that Android-powered devices use to install the application.

**Answer**: **APK (`.apk` / Android Package)**

---

### 3. ****\_\_\_**** is the minimum version of the JDK to be installed for Android studio set up.

**Answer**: **JDK 8** (Java Development Kit 8 / 1.8) _(Note: Modern Android Studio releases bundle OpenJDK 11/17 internally)._

---

### 4. List any two mobile operating systems other than Android.

**Answer**:

1. **iOS** (Apple)
2. **KaiOS** (or Windows Phone / BlackBerry OS)

---

### 5. Below are the contents of the `strings.xml` file. Here add a new string named `"edit_message"` and set the value to `"Enter a message."`

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">AndroidApp</string>
    <string name="edit_message">Enter a message.</string>
</resources>
```

---

### 6. Mention the different ways to distribute Android apps.

1. **Google Play Store**: Official primary distribution platform.
2. **Third-Party App Stores**: Amazon Appstore, Samsung Galaxy Store.
3. **Direct Sideloading / Web Download**: Host `.apk` or `.aab` bundles directly on a website for manual installation.

---

### 7. Categorize the contents of the `res` directory in the Android project structure.

1. **`res/layout/`**: XML UI screen layout files.
2. **`res/drawable/`**: Images (PNG, JPEG) and Vector XML drawables.
3. **`res/values/`**: XML value resources (`strings.xml`, `colors.xml`, `dimens.xml`, `styles.xml`).
4. **`res/mipmap/`**: App launcher icon files for different screen density buckets.
5. **`res/menu/`**: Options and context menu XML definitions.

---

### 8. Classify at least four Android versions in the decreasing order starting from the latest release. Also specify the Android mobile device for each of the version.

| Decreasing Order | Android Version (Code Name)       | Release API Level | Representative Mobile Device |
| :--------------- | :-------------------------------- | :---------------- | :--------------------------- |
| **1 (Latest)**   | **Android 14** (Upside Down Cake) | API 34            | Google Pixel 8 / Pixel 8 Pro |
| **2**            | **Android 13** (Tiramisu)         | API 33            | Samsung Galaxy S23 Ultra     |
| **3**            | **Android 12** (Snow Cone)        | API 31 / 32       | Google Pixel 6 / 6 Pro       |
| **4**            | **Android 11** (Red Velvet Cake)  | API 30            | OnePlus 9 Pro                |

---

### 9. The \_\_\_\_ attribute in the manifest file sets the app icon.

**Answer**: **`android:icon`** (e.g. `android:icon="@mipmap/ic_launcher"`)

---

### 10. To create a linear layout in which each child view uses the same amount of space on the screen, set the `android:layout_height` of each view to \_**\_ (for a vertical layout). Then set the `android:layout_weight` of each view to \_\_\_\_**.

**Answer**: First blank: **`"0dp"`** | Second blank: **`"1"`** (or equal float numbers like `1.0`).
