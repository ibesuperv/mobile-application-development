# Question Paper Solutions: Paper-3 Part-A (Short Answers 1.1 to 1.11)

---

### 1.1 "It is recommended to create string resource instead of placing strings directly into layouts". Justify. (1 Mark)

**Justification**: Extracting text into string resources (`res/values/strings.xml`) allows for easy **localization/internationalization** (translating apps into multiple languages without modifying UI code), enables **reusability** across multiple views, and ensures consistent text maintenance.

---

### 1.2 For Android apps, layouts, string values, colors and other resources are primarily defined in \_**\_ files. These files are stored in the \_\_** directory. (2 Marks)

**Answer**: First blank: **XML (`.xml`)** | Second blank: **`res/`** (Resources directory).

---

### 1.3 **\_\_** represents a single user interface screen. (1 Mark)

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

**Answer**: **Android 14** (API Level 34 / Code name: _Upside Down Cake_).

---

### 1.7 **\_\_** method is used to access a view element of a layout resource in an activity. (1 Mark)

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

---

# Question Paper Solutions: Paper-4 Part-A (Short Answers 1.1 to 1.10)

---

### 1.1 "It is recommended to create string resource instead of placing strings directly into layouts". Justify. (1 Mark)

**Justification**: Extracting strings into resource files (`res/values/strings.xml`) allows for easy **app localization/translation** into multiple languages without modifying UI layout XML files, promotes **code reusability**, and ensures consistent text management across the application.

---

### 1.2 For Android apps, layouts, string values, colors and other resources are primarily defined in \_**\_ files. These files are stored in the \_\_** directory. (1 Mark)

**Answer**: First blank: **`XML`** | Second blank: **`res/`** (Resources directory).

---

### 1.3 Edit the **\_\_\_\_** file to add features components and permissions to your Android app. All components for an app, such as multiple activities, must be declared in this XML file. (1 Mark)

**Answer**: **`AndroidManifest.xml`**

---

### 1.4 Write the complete path of the file to navigate, for changing the visual display of the android app. (1 Mark)

**Answer**: **`app/src/main/res/layout/activity_main.xml`**

---

### 1.5 Write the contents of the `strings.xml` file to add a new string property called "PressButton" and give it a value of "Android ISE!" (1 Mark)

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">AndroidApp</string>
    <string name="PressButton">Android ISE!</string>
</resources>
```

---

### 1.6 A good choice for a View within a ScrollView is a **\_\_\_** that is arranged in a vertical orientation. (1 Mark)

**Answer**: **`LinearLayout`**

---

### 1.7 **\_\_** method is used to access a view element of a layout resource in an activity. (1 Mark)

**Answer**: **`findViewById()`**

---

### 1.8 Write the code segment to include logging statement in app to see the statement "MainActivity layout is complete" in the Logcat pane. (1 Mark)

```java
Log.d("MainActivity", "MainActivity layout is complete");
```

---

### 1.9 Differentiate between focusable and clickable views in Android. (1 Mark)

- **`android:focusable="true"`**: Specifies whether a view can gain input focus from a physical keyboard, D-pad, or trackball navigation (crucial for TV and accessibility).
- **`android:clickable="true"`**: Specifies whether a view responds to direct user touch/click events (triggering `setOnClickListener`).

---

### 1.10 In Android Studio illustrate how to create a virtual device (emulator) to run the app. (1 Mark)

1. Open **Tools > Device Manager** in Android Studio.
2. Click **Create Virtual Device**.
3. Select a **Hardware Profile** (e.g. Pixel 6) and click **Next**.
4. Select & download an Android **System Image** (e.g. API 33 `x86_64`) and click **Finish**.
5. Select the emulator from the device drop-down and click **Run (`Shift + F10`)**.
