# Question Paper Solutions: Paper-3 Part-A (Short Answers 1.1 to 1.11)

---

### 1.1 "It is recommended to create string resource instead of placing strings directly into layouts". Justify. (1 Mark)
**Justification**: Extracting text into string resources (`res/values/strings.xml`) allows for easy **localization/internationalization** (translating apps into multiple languages without modifying UI code), enables **reusability** across multiple views, and ensures consistent text maintenance.

---

### 1.2 For Android apps, layouts, string values, colors and other resources are primarily defined in ____ files. These files are stored in the ____ directory. (2 Marks)
**Answer**: First blank: **XML (`.xml`)** | Second blank: **`res/`** (Resources directory).

---

### 1.3 ______ represents a single user interface screen. (1 Mark)
**Answer**: **Activity**

---

### 1.4 Write the complete path of the file to navigate, for changing the visual display of the android app. (1 Mark)
**Answer**: **`app/src/main/res/layout/activity_main.xml`**

---

### 1.5 Write the contents of the `strings.xml` file to add a new string property called "PressButton" and give it a value of "6 th semester ISE!" (2 Marks)
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">AndroidApp</string>
    <string name="PressButton">6 th semester ISE!</string>
</resources>
```

---

### 1.6 Mention the latest release of Android version. (1 Mark)
**Answer**: **Android 14** (API Level 34 / Code name: *Upside Down Cake*).

---

### 1.7 ______ method is used to access a view element of a layout resource in an activity. (1 Mark)
**Answer**: **`findViewById()`** (e.g. `findViewById(R.id.my_view)`).

---

### 1.8 Name the method which is called first when an Activity is initially launched. (1 Mark)
**Answer**: **`onCreate()`**

---

### 1.9 Output Screen Description for XML Layout (2 Marks)

```xml
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <TextView 
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true"
        android:text="WELCOME TO THE WORLD OF ANDROID" />
</RelativeLayout>
```

#### Output (Activity Screen Mockup):
The screen renders a single TextView displayed **perfectly centered both horizontally and vertically** in the exact middle of the screen.

```
+------------------------------------------+
|                                          |
|                                          |
|  WELCOME TO THE WORLD OF ANDROID         |
|             (Center of Screen)           |
|                                          |
|                                          |
+------------------------------------------+
```

---

### 1.10 The software components defined in Android applications are : (1 Mark)
**Answer**: 
1. **Activity**
2. **Service**
3. **Broadcast Receiver**
4. **Content Provider**

---

### 1.11 Illustrate the importance of minimum SDK version (`minSdkVersion`) in choosing target Android device during app development. (2 Marks)
**Importance**:
- **`minSdkVersion`** defines the lowest Android OS API level on which the app can run.
- The Google Play Store uses `minSdkVersion` to block users with older Android OS versions from installing the app if their device doesn't support the required APIs.
- Setting a higher `minSdkVersion` gives access to newer features but reduces the device audience pool, whereas a lower `minSdkVersion` increases device compatibility.
