# Question Paper Solutions: Part-A (Short Fill-in & 1-2 Mark Questions)

---

### 1. The Android SDK separates tools, platforms, and other components into packages which can be downloaded as needed using the _____________.
**Answer**: **SDK Manager**

---

### 2. Name the type of file that Android-powered devices use to install the application.
**Answer**: **APK (`.apk` / Android Package)**

---

### 3. ___________ is the minimum version of the JDK to be installed for Android studio set up.
**Answer**: **JDK 8** (Java Development Kit 8 / 1.8) *(Note: Modern Android Studio releases bundle OpenJDK 11/17 internally).*

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

| Decreasing Order | Android Version (Code Name) | Release API Level | Representative Mobile Device |
| :--- | :--- | :--- | :--- |
| **1 (Latest)** | **Android 14** (Upside Down Cake) | API 34 | Google Pixel 8 / Pixel 8 Pro |
| **2** | **Android 13** (Tiramisu) | API 33 | Samsung Galaxy S23 Ultra |
| **3** | **Android 12** (Snow Cone) | API 31 / 32 | Google Pixel 6 / 6 Pro |
| **4** | **Android 11** (Red Velvet Cake) | API 30 | OnePlus 9 Pro |

---

### 9. The ____ attribute in the manifest file sets the app icon.
**Answer**: **`android:icon`** (e.g. `android:icon="@mipmap/ic_launcher"`)

---

### 10. To create a linear layout in which each child view uses the same amount of space on the screen, set the `android:layout_height` of each view to ____ (for a vertical layout). Then set the `android:layout_weight` of each view to ______.
**Answer**: First blank: **`"0dp"`** | Second blank: **`"1"`** (or equal float numbers like `1.0`).
