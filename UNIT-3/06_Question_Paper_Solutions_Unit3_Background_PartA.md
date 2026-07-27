# Question Paper Solutions: Background Tasks & Security Paper (Part-A Questions 1 to 6)

---

### 1. Define an AsyncTask? (1 Mark)
**Answer**: An **`AsyncTask`** is an abstract Android class that enables short background operations (a few seconds) off the main UI thread and publishes results back on the UI thread without requiring manual thread or handler management.

---

### 2. What is a LoaderManager? (2 Marks)
**Answer**: **`LoaderManager`** is an abstract class associated with an `Activity` or `Fragment` used to manage one or more `Loader` instances. It handles the lifecycle of loaders, starting, stopping, retaining, and destroying them automatically across screen rotations.

---

### 3. List the types of Broadcasts. Give an example for each. (2 Marks)
1. **Normal (Unordered) Broadcasts**: Delivered to all registered receivers simultaneously in an unpredictable order.
   - *Example*: `Intent.ACTION_POWER_CONNECTED` (Sent when charger is plugged in).
2. **Ordered Broadcasts**: Delivered to one receiver at a time in priority order, allowing higher priority receivers to abort or modify the broadcast.
   - *Example*: `Intent.ACTION_NEW_OUTGOING_CALL` (Sent when user dials a call).
3. **Sticky Broadcasts** (Deprecated): Retained by the system after completion so future receivers can retrieve the cached data immediately upon registration.
   - *Example*: `Intent.ACTION_BATTERY_CHANGED`.

---

### 4. Define Intent Filter. (1 Mark)
**Answer**: An **Intent Filter** is an expression declared in `AndroidManifest.xml` (or code) specifying the types of implicit intents an application component (`Activity`, `Service`, `BroadcastReceiver`) is willing to receive based on intent action, data, and category.

---

### 5. Differentiate between Shared Preferences and Saved Instance State. (2 Marks)

| Dimension | SharedPreferences | SavedInstanceState |
| :--- | :--- | :--- |
| **Persistence Horizon** | **Permanent**; saved to XML file on disk across app kills and device reboots. | **Transient**; stored in RAM memory during the current Activity lifecycle session. |
| **Best Use Case** | User settings, app configurations, login tokens, high scores. | Preserving UI form input (e.g. typed text) during screen rotations. |
| **Data Format** | Key-value pairs stored in `/data/data/<pkg>/shared_prefs/`. | Key-value pairs stored in a `Bundle` object in RAM. |

---

### 6. While Registering the Listener during the implementation of SharedPreferences – You need a strong Reference. Justify. (2 Marks)
**Justification**: `SharedPreferences.registerOnSharedPreferenceChangeListener()` stores listeners in a **WeakHashMap**. If you pass an anonymous listener or store it in a local variable, the Java Garbage Collector (GC) will reclaim and garbage-collect the listener instance when the method finishes, causing preference change callbacks to silently stop firing. Maintaining a **strong field reference** inside the Activity/Fragment class prevents garbage collection.
