# Scheduling Alarms & Transferring Data Efficiently

---

## SECTION 1: Scheduling Alarms (`AlarmManager`)

An **Alarm** (`AlarmManager`) is an Android system service that allows you to schedule tasks to run at specific clock times or elapsed intervals outside the normal lifecycle of your application.

```
                         ALARM ARCHITECTURE
                                  |
     +----------------------------+----------------------------+
     |                                                         |
     v                                                         v
ELAPSED REAL-TIME ALARMS                                REAL-TIME CLOCK (RTC) ALARMS
(Time elapsed since device boot)                        (Wall-clock UTC time & date)
- ELAPSED_REALTIME                                      - RTC
- ELAPSED_REALTIME_WAKEUP                               - RTC_WAKEUP
```

> [!IMPORTANT]
> **When NOT to use `AlarmManager`**:
> - For UI timers, delays, or timeouts during active app execution $\rightarrow$ Use `Handler` with `postDelayed()`.
> - For server synchronization tasks $\rightarrow$ Use `WorkManager` or `SyncAdapter`.
> - For deferrable batch jobs contingent on Wi-Fi/Charging $\rightarrow$ Use `JobScheduler`.

---

### 1. The 4 Core Alarm Types (Exam 6-Mark Question)

| Alarm Type Constant | Time Reference Basis | CPU Wakeup Behavior | Best Use Case |
| :--- | :--- | :--- | :--- |
| **`ELAPSED_REALTIME`** | Time elapsed since device boot (includes sleep time). | Does **NOT** wake the device CPU up. Fires next time device wakes. | Interval tasks where exact wall-clock time doesn't matter (e.g. check every 30 mins). |
| **`ELAPSED_REALTIME_WAKEUP`** | Time elapsed since device boot. | **Wakes device CPU** up from sleep if screen is off. | Time-sensitive elapsed intervals that must execute even if screen is off. |
| **`RTC`** | Coordinated Universal Time (UTC / Wall Clock). | Does **NOT** wake the device CPU up. | Clock-based reminders where waking CPU is unnecessary. |
| **`RTC_WAKEUP`** | Coordinated Universal Time (UTC / Wall Clock). | **Wakes device CPU** up from sleep. | User-facing calendar reminders or alarm clock rings (e.g. 7:00 AM alarm). |

---

### 2. Single-Use vs. Inexact Repeating Alarms

#### A. Single-Use Alarms:
- **`set()`**: Schedules an inexact single-use alarm on API 19+ (system batches it to conserve battery).
- **`setExact()`**: Guarantees alarm delivery at the exact requested time (API 19+). Use sparingly.
- **`setExactAndAllowWhileIdle()`**: Triggers exact alarm even if device is in **Doze Mode**.

#### B. Repeating Alarms:
- **`setInexactRepeating()`**: Standard repeating alarm on API 19+. Android automatically synchronizes and **batches repeating alarms from multiple apps**, reducing total CPU wakeups.

---

### 3. Complete Executable Code Pattern: `AlarmManager` & `BroadcastReceiver`

#### Step 1: Create the Receiver (`AlarmReceiver.java`)
```java
public class AlarmReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        // Trigger notification or background work when alarm fires
        Toast.makeText(context, "Alarm Triggered!", Toast.LENGTH_SHORT).show();
    }
}
```

#### Step 2: Schedule and Cancel Alarms (`MainActivity.java`)
```java
public class MainActivity extends AppCompatActivity {

    private AlarmManager mAlarmManager;
    private PendingIntent mNotifyPendingIntent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mAlarmManager = (AlarmManager) getSystemService(ALARM_SERVICE);

        // Create PendingIntent targeting AlarmReceiver
        Intent notifyIntent = new Intent(this, AlarmReceiver.class);
        mNotifyPendingIntent = PendingIntent.getBroadcast(
            this, 
            0, 
            notifyIntent, 
            PendingIntent.FLAG_UPDATE_CURRENT
        );
    }

    // Schedule Inexact Repeating Alarm (Every 15 minutes)
    public void startRepeatingAlarm() {
        long triggerAtMillis = SystemClock.elapsedRealtime() + AlarmManager.INTERVAL_FIFTEEN_MINUTES;

        if (mAlarmManager != null) {
            mAlarmManager.setInexactRepeating(
                AlarmManager.ELAPSED_REALTIME_WAKEUP,
                triggerAtMillis,
                AlarmManager.INTERVAL_FIFTEEN_MINUTES,
                mNotifyPendingIntent
            );
        }
    }

    // Check if Alarm is Already Active using FLAG_NO_CREATE (Exam 4-Mark Trick)
    public boolean isAlarmSet() {
        Intent notifyIntent = new Intent(this, AlarmReceiver.class);
        return (PendingIntent.getBroadcast(
            this, 0, notifyIntent, PendingIntent.FLAG_NO_CREATE) != null);
    }

    // Cancel Alarm
    public void cancelAlarm() {
        if (mAlarmManager != null) {
            mAlarmManager.cancel(mNotifyPendingIntent);
            mNotifyPendingIntent.cancel();
        }
    }
}
```

---

### 4. Impact of Battery Saver Modes: Doze & App Standby (Exam Concept)

- **Doze Mode**: Activated when device is unplugged, stationary, and screen is off for a period of time. Defers background jobs and standard alarms to periodic **maintenance windows**.
- **App Standby**: Restricts apps that haven't been used recently.
- **Handling Doze**: Use `setAndAllowWhileIdle()` or `setAlarmClock()` (for user-visible alarm clocks) if execution cannot wait for maintenance windows.

---

## SECTION 2: Transferring Data Efficiently

Networking is one of the highest sources of mobile battery drain due to the **Wireless Radio State Machine**.

---

### 1. The Wireless Radio State Machine & Tail Time (Exam 8-Mark Question)

```
        STANDBY STATE  ─────────── (2s Latency) ───────────>  FULL POWER STATE
        (Minimal Power)                                      (Highest Transfer Rate)
               ^                                                        │
               │                                                        │
          (12s Delay)                                              (5s Tail Time)
               │                                                        │
               └─────────── LOW POWER STATE <───────────────────────────┘
                            (50% Power / 1.5s Latency)
```

#### Tail Time Battery Cost:
- When a data transfer finishes, the radio does NOT turn off immediately. It stays at **Full Power for ~5 seconds ("Tail Time")**, then drops to **Low Power for ~12 seconds** before returning to Standby.
- **The Problem**: An app transmitting 1 second of unbundled data every 18 seconds keeps the cellular radio **permanently powered on**, destroying battery life!

---

### 2. Network Efficiency Strategies

#### A. Bundling & Batching Data Transfers
Group multiple small network requests together into a single combined burst session. 
- **Result**: Paying the ~20-second radio startup and tail time cost **once** instead of dozens of times throughout the day.

#### B. Data Prefetching
Guess what content the user will need next (e.g. next news articles, next video chunk) and download it in a single high-speed burst.
- **Rule of Thumb**: Prefetch 1-5 MB of data every 2-5 minutes on unmetered Wi-Fi.

#### C. Network-Aware Prefetching (`ConnectivityManager` & `TelephonyManager`)
Adjust prefetch cache size based on current network connection type:

```java
ConnectivityManager cm = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
TelephonyManager tm = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);
NetworkInfo activeNetwork = cm.getActiveNetworkInfo();

int prefetchCacheSize = 1024; // Default 1MB

if (activeNetwork != null && activeNetwork.isConnected()) {
    switch (activeNetwork.getType()) {
        case ConnectivityManager.TYPE_WIFI:
            prefetchCacheSize = 4096; // 4MB on Wi-Fi (aggressive prefetch)
            break;
        case ConnectivityManager.TYPE_MOBILE:
            switch (tm.getNetworkType()) {
                case TelephonyManager.NETWORK_TYPE_LTE:
                    prefetchCacheSize = 2048; // 2MB on 4G LTE
                    break;
                case TelephonyManager.NETWORK_TYPE_EDGE:
                case TelephonyManager.NETWORK_TYPE_GPRS:
                    prefetchCacheSize = 512; // 512KB on 2G/Slow networks
                    break;
            }
            break;
    }
}
```

#### D. Battery-Aware Operations (`BatteryManager`)
Check device charging state before launching heavy sync tasks:

```java
IntentFilter ifilter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
Intent batteryStatus = context.registerReceiver(null, ifilter);

int status = batteryStatus.getIntExtra(BatteryManager.EXTRA_STATUS, -1);
boolean isCharging = (status == BatteryManager.BATTERY_STATUS_CHARGING ||
                      status == BatteryManager.BATTERY_STATUS_FULL);
```
