# Question Paper Solutions: Part-B Units 3, 4 & 5 (Questions 6a to 10b)

---

## Question 6a: AsyncTask Execution Steps, Usage & Parameters (8 Marks)

### A. Execution Steps & Callback Execution Flow (4 Marks)
**`AsyncTask`** is an abstract class used to perform short background operations (up to a few seconds) and publish results on the UI thread without manually handling threads or handlers.

```
                           ASYNCTASK EXECUTION FLOW
                                      │
  (UI Thread)                     onPreExecute()
                                      │
  (Worker Thread)              doInBackground()  ────>  publishProgress()
                                      │                        │
  (UI Thread)                     onPostExecute() <───  onProgressUpdate()
```

1. **`onPreExecute()` (UI Thread)**: Executed before background work starts. Used to setup UI elements (e.g., showing a `ProgressBar`).
2. **`doInBackground(Params...)` (Worker Thread)**: Executed automatically on a dedicated worker thread. Handles heavy CPU or network tasks. Calls `publishProgress()` to send updates.
3. **`onProgressUpdate(Progress...)` (UI Thread)**: Triggered after `publishProgress()` is called. Updates UI progress bars.
4. **`onPostExecute(Result)` (UI Thread)**: Executed after `doInBackground()` finishes. Receives the final computation result to update UI views.

---

### B. AsyncTask 3 Generic Parameters (4 Marks)
`public class MyTask extends AsyncTask<Params, Progress, Result>`

1. **`Params`**: Type of input data sent to `doInBackground()` upon calling `task.execute(Params)`.
2. **`Progress`**: Type of progress units published to `onProgressUpdate()` during background work.
3. **`Result`**: Type of final output result returned by `doInBackground()` and delivered to `onPostExecute()`.

---

## Question 6b: Android App Code for Notifications (.xml & .java) (8 Marks)

### Layout File (`activity_main.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <Button
        android:id="@+id/btn_notify"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="SEND NOTIFICATION" />

</LinearLayout>
```

### Java Implementation (`MainActivity.java`)
```java
package com.example.notificationapp;

import android.app.NotificationChannel;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.widget.Button;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.NotificationCompat;

public class MainActivity extends AppCompatActivity {

    private static final String CHANNEL_ID = "PRIMARY_NOTIF_CHANNEL";
    private static final int NOTIFICATION_ID = 101;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        createNotificationChannel();

        Button btnNotify = findViewById(R.id.btn_notify);
        btnNotify.setOnClickListener(v -> sendNotification());
    }

    private void createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationChannel channel = new NotificationChannel(
                    CHANNEL_ID, "App Notifications", NotificationManager.IMPORTANCE_DEFAULT);
            NotificationManager manager = getSystemService(NotificationManager.class);
            if (manager != null) manager.createNotificationChannel(channel);
        }
    }

    private void sendNotification() {
        Intent intent = new Intent(this, MainActivity.class);
        PendingIntent pendingIntent = PendingIntent.getActivity(
                this, 0, intent, PendingIntent.FLAG_UPDATE_CURRENT | PendingIntent.FLAG_IMMUTABLE);

        NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                .setSmallIcon(R.drawable.ic_notification)
                .setContentTitle("New Alert!")
                .setContentText("This is an exam test notification.")
                .setContentIntent(pendingIntent)
                .setAutoCancel(true);

        NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
        if (manager != null) manager.notify(NOTIFICATION_ID, builder.build());
    }
}
```

---

## Question 7a: Loaders & Callbacks in Starting & Restarting Them (8 Marks)

### A. What is a Loader? (3 Marks)
A **Loader** (`AsyncTaskLoader` / `CursorLoader`) is an Android framework component used to load data asynchronously off the main thread.
- **Key Advantages**: Automatically monitors data sources, retains cached data across screen rotations, and prevents memory leaks.

---

### B. `LoaderManager.LoaderCallbacks` Callbacks (5 Marks)

1. **`onCreateLoader(int id, Bundle args)`**:
   - Called when system initializes or restarts a loader. Returns a new `Loader` instance.
2. **`onLoadFinished(Loader<D> loader, D data)`**:
   - Called when a previously created loader finishes its data load. Used to update UI views (e.g. `adapter.swapCursor(data)`).
3. **`onLoaderReset(Loader<D> loader)`**:
   - Called when a created loader is being reset, making its data unavailable. Used to clear references to the data.

#### Starting vs. Restarting Loaders:
- **`initLoader(id, args, callbacks)`**: Initializes a new loader or re-uses an existing active loader.
- **`restartLoader(id, args, callbacks)`**: Discards existing loader data and creates a fresh loader instance to query updated data.

---

## Question 7b: Parameters for Efficient Data Transfer (8 Marks)

Networking is a primary source of battery drain due to the **Wireless Radio State Machine** and **Tail Time**.

### 4 Parameters for Efficient Data Transfer:

1. **Cellular Radio State Machine & Tail Time**:
   - Radio remains at **Full Power for ~5 seconds (Tail Time)** and **Low Power for ~12 seconds** after data finishes transmitting.
   - **Optimization**: Bundle small requests together so the 20-second power draw cost is paid **once** instead of repeatedly.
2. **Data Bundling & Batching**:
   - Grouping multiple queued transfers to occur simultaneously during a single radio wakeup window.
3. **Prefetching**:
   - Anticipating user actions and downloading 1-5 MB of upcoming data in a single burst on unmetered Wi-Fi.
4. **Network & Battery Awareness (`ConnectivityManager` & `BatteryManager`)**:
   - Adjusting prefetch size based on connection type (Wi-Fi vs LTE vs 2G) and checking charging state (`isCharging`) before executing heavy background syncs.

---

## Question 8a: Shared Preferences Functionality & Classification (6 Marks)

### A. Functionality (3 Marks)
**`SharedPreferences`** is a lightweight persistent storage mechanism that allows apps to read and write key-value pairs of primitive data types (`int`, `boolean`, `String`) to an XML file stored in private application memory (`/data/data/<pkg>/shared_prefs/`). Data persists across app kills and reboots.

---

### B. Classification / Acquisition Methods (3 Marks)

1. **`getSharedPreferences(fileName, MODE_PRIVATE)`**:
   - Used when you need **multiple preference files** identified by specific custom file names.
2. **`getPreferences(MODE_PRIVATE)`**:
   - Used from an `Activity` when you need **only one preference file** dedicated exclusively to that specific Activity.
3. **`PreferenceManager.getDefaultSharedPreferences(context)`**:
   - Used to get the **app-wide default preference file** shared across the entire application (used alongside Preference UI screens).

---

## Question 8b: Location-Based Services Components & Objects (10 Marks)

### A. Major Components of Location-Based Services (6 Marks)

1. **`FusedLocationProviderClient`**:
   - Primary API entry point for managing location requests, combining GPS, Wi-Fi, and cellular signals for optimal accuracy and battery efficiency.
2. **Location Permissions (`ACCESS_FINE_LOCATION` & `ACCESS_COARSE_LOCATION`)**:
   - Manifest permissions required to access high-accuracy (GPS) or low-accuracy (Wi-Fi/Cell) device coordinates.
3. **Location Listener / Callback (`LocationCallback`)**:
   - Receives continuous location update events when the device location changes.

---

### B. List of 6 Location Objects (4 Marks)

1. **`Location`**: Data object containing latitude, longitude, altitude, speed, bearing, and timestamp.
2. **`LatLng`**: Represents a geographical point with exact latitude and longitude values.
3. **`LocationRequest`**: Specifies parameters for location updates (priority, update interval, fastest interval).
4. **`LocationResult`**: Contains the latest list of location updates returned by `FusedLocationProviderClient`.
5. **`Geocoder`**: Converts coordinates to physical street addresses (Reverse Geocoding) or addresses to coordinates (Geocoding).
6. **`Address`**: Represents a physical postal address string returned by `Geocoder`.

---

## Question 9a: Elapsed Real-Time Alarms vs. Real-Time Clock Alarms (6 Marks)

Both alarm types use `AlarmManager` to broadcast intents outside the app lifecycle:

```
                         ALARM TYPES COMPARISON
                                    |
     +------------------------------+------------------------------+
     |                                                             |
     v                                                             v
ELAPSED REAL-TIME ALARMS                                REAL-TIME CLOCK (RTC) ALARMS
(Based on time elapsed since device boot)               (Based on wall-clock UTC time & date)
- ELAPSED_REALTIME                                      - RTC
- ELAPSED_REALTIME_WAKEUP                               - RTC_WAKEUP
```

| Dimension | Elapsed Real-Time Alarms | Real-Time Clock (RTC) Alarms |
| :--- | :--- | :--- |
| **Time Basis** | Milliseconds elapsed since device boot (includes sleep time). | Coordinated Universal Time (UTC / Wall Clock time). |
| **Locale / Timezone Sensitivity** | **Not affected** by time zone changes or clock edits. | **Affected** by user changing system time or time zone. |
| **Variants** | `ELAPSED_REALTIME` (Doesn't wake CPU), `ELAPSED_REALTIME_WAKEUP` (Wakes CPU). | `RTC` (Doesn't wake CPU), `RTC_WAKEUP` (Wakes CPU). |
| **Best Use Case** | Interval tasks (e.g. trigger task every 30 mins). | Wall-clock events (e.g. ring alarm clock at 7:00 AM). |

---

## Question 9b: Callback Methods of `SQLiteOpenHelper` Class (10 Marks)

`SQLiteOpenHelper` is an abstract helper class used to manage SQLite database creation and versioning.

### Mandatory & Optional Callback Methods:

1. **`onCreate(SQLiteDatabase db)` (Mandatory)**:
   - Called when the database is created for the **very first time**. Used to execute `db.execSQL()` statements creating initial tables and indexes.
2. **`onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)` (Mandatory)**:
   - Called automatically when `DATABASE_VERSION` is increased. Used to perform schema migration (e.g. `DROP TABLE IF EXISTS` or `ALTER TABLE`).
3. **`onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion)` (Optional)**:
   - Called when `DATABASE_VERSION` is decreased. Default implementation rejects downgrade and throws `SQLiteException`.
4. **`onOpen(SQLiteDatabase db)` (Optional)**:
   - Called after the database connection has been opened, before `onCreate()` or `onUpgrade()`. Used to enable foreign key constraints (`PRAGMA foreign_keys=ON`).
5. **`onConfigure(SQLiteDatabase db)` (Optional)**:
   - Called when the database connection is being configured. Used to enable Write-Ahead Logging (WAL).

---

## Question 10a: Types of Permissions & 3 Best Practices (8 Marks)

### A. Types of Permissions in Android (4 Marks)

1. **Normal Permissions**: Low risk to user privacy/data (e.g. `INTERNET`, `ACCESS_NETWORK_STATE`, `VIBRATE`). **Auto-granted** by Android at install time.
2. **Dangerous Permissions**: High risk; accesses private user data or sensitive hardware (e.g. `CAMERA`, `READ_CONTACTS`, `LOCATION`, `SEND_SMS`). **Requires explicit runtime user dialog approval** on API 23+.
3. **Signature Permissions**: Granted automatically only if the requesting app is signed with the same digital certificate as the declaring app.

---

### B. 3 Best Practices for Permissions (4 Marks)

1. **Request Minimum Required Permissions**: Only ask for permissions essential to core functionality.
2. **Use Implicit Intents Instead of Direct Hardware Permissions**: Instead of asking for `CAMERA` permission, send an `ACTION_IMAGE_CAPTURE` intent to let the system camera handle capture.
3. **Provide Context Before Requesting Runtime Permissions**: Explain to the user why a dangerous permission is needed before displaying the system permission dialog.

---

## Question 10b: Steps to Publish an Android App (8 Marks)

```
                       APP PUBLISHING WORKFLOW
                                   │
  1. Remove Debug Code & Set versionCode/versionName in build.gradle
                                   │
  2. Generate Signed Android App Bundle (AAB) with Keystore (.jks)
                                   │
  3. Create Google Play Console Developer Account ($25 fee)
                                   │
  4. Create Store Listing (Screenshots, Description, Privacy Policy URL)
                                   │
  5. Upload AAB to Production Track & Submit for Google Review
```

1. **App Polishing**: Remove debug logs (`Log.d`), set `versionCode` (integer) and `versionName` (string) in `build.gradle`.
2. **Generate Signed Release Bundle**: Select **Build > Generate Signed Bundle / APK** in Android Studio. Create a secure **Keystore (`.jks`)** file and export a signed `.aab` (Android App Bundle).
3. **Play Console Registration**: Create a Google Play Developer Account ($25 one-time fee).
4. **Store Listing Preparation**: Provide app title, short/long description, app icon, promo graphics, screenshots, and mandatory **Privacy Policy URL**.
5. **Content Rating & Pricing**: Complete the content rating questionnaire and set pricing (Free or Paid).
6. **Upload & Review**: Upload the signed `.aab` file to the Production Track and submit for automated and manual Google Play Store review.
