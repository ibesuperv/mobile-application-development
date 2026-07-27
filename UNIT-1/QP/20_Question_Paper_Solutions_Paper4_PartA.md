# Question Paper Solutions: Paper-4 Part-A (Short Answers 1.1 to 1.10)

---

### 1.1 "It is recommended to create string resource instead of placing strings directly into layouts". Justify. (1 Mark)
**Justification**: Extracting strings into resource files (`res/values/strings.xml`) allows for easy **app localization/translation** into multiple languages without modifying UI layout XML files, promotes **code reusability**, and ensures consistent text management across the application.

---

### 1.2 For Android apps, layouts, string values, colors and other resources are primarily defined in ____ files. These files are stored in the ____ directory. (1 Mark)
**Answer**: First blank: **`XML`** | Second blank: **`res/`** (Resources directory).

---

### 1.3 Edit the ________ file to add features components and permissions to your Android app. All components for an app, such as multiple activities, must be declared in this XML file. (1 Mark)
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

### 1.6 A good choice for a View within a ScrollView is a _______ that is arranged in a vertical orientation. (1 Mark)
**Answer**: **`LinearLayout`**

---

### 1.7 ______ method is used to access a view element of a layout resource in an activity. (1 Mark)
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
