# UNIT-3: Complete Study Index & Exam Syllabus Map

---

## 📌 Unit-3 Syllabus Overview

> **Syllabus**:
> Working in the background: Async Task and Async Task Loader, Connect to the Internet, Broadcast Receivers, Services, Scheduling and optimizing background tasks – Notifications, Scheduling Alarms, and Transferring Data Efficiently.

---

## 📚 Recommended Sequential Reading Order

To study Unit-3 systematically for your examination, read the generated note files in the following exact sequence:

```
[Step 1] 01_AsyncTask_and_AsyncTaskLoader.md
   │     └─ Main Thread rules, ANR, AsyncTask (4 callbacks, generics, flaws), AsyncTaskLoader (LoaderManager, 3 callbacks).
   ▼
[Step 2] 02_Connect_to_Internet_and_Broadcast_Receivers.md
   │     └─ Manifest permissions, HttpURLConnection, Uri.Builder, JSON parsing, ConnectivityManager, Broadcast Receivers (Static vs Dynamic), LocalBroadcastManager.
   ▼
[Step 3] 03_Services_Scheduling_and_Notifications.md
   │     └─ 3 Service Types (Foreground, Started, Bound with diagram), IntentService, JobScheduler vs AlarmManager vs WorkManager, Notification Channels (API 26+), PendingIntent.
   ▼
[Step 4] 04_Scheduling_Alarms_and_Efficient_Data_Transfer.md
         └─ AlarmManager (4 Alarm Types, setInexactRepeating, FLAG_NO_CREATE, Doze Mode), Wireless Radio State Machine (Tail Time), Bundling, Prefetching, Network/Battery awareness.

─── 📄 QUESTION PAPER SOLUTIONS (STORED IN MAIN & QP/ SUBDIRECTORIES) ───

[Step 5] 04_Question_Paper_Solutions_Unit3.md
   │     └─ 2020-21 Q3 (Implicit vs Explicit Receivers, System vs App Events, Email sending code), 2020-21 Q4 (CountDownTimer complete XML & Java code), 2020-21 Q5 (Activities, Intents, Parameter passing Activity1 -> Activity2 complete code).
   ▼
[QP 05] QP/05_Question_Paper_Solutions_Units3_4_5.md
   │     └─ Q6a (AsyncTask execution flow & generics), Q6b (Notification app code), Q7a (Loaders & LoaderCallbacks), Q7b (Data Transfer parameters & Tail Time), Q8a (SharedPreferences & acquisition methods), Q8b (Location-Based Services & 6 location objects), Q9a (Elapsed Real-Time vs RTC Alarms), Q9b (SQLiteOpenHelper callbacks), Q10a (Permissions types & 3 best practices), Q10b (App Publishing steps).
   ▼
[QP 06] QP/06_Question_Paper_Solutions_Unit3_Background_PartA.md
   │     └─ Background & Security Part-A (AsyncTask definition, LoaderManager, Broadcast types & examples, Intent Filter definition, SharedPreferences vs SavedInstanceState, Strong reference justification).
   ▼
[QP 07] QP/07_Question_Paper_Solutions_Unit3_Background_PartB.md
   │     └─ Background & Security Part-B (Q1 App Publishing steps, Q2 AsyncTaskLoader diagram & code, Q3 Permission grant/revoke/types/best practices, Q4 BroadcastReceiver implementation steps, Q5 App Security practices).
   ▼
[QP 08] QP/08_Question_Paper_Solutions_Unit3_Broadcasts_Paper.md
         └─ Quiz 1-5 (Broadcasts definition, sendBroadcast methods, Up vs Back navigation, 4 mobile services, Simulator vs Emulator differences) + Part B Q1a-b, Q2a-b (Intents comparison & code), Q3 (Power Connection Broadcast Receiver Java & Manifest code), Q4 (Services Lifecycle diagram & explanation), Q5 (Firebase/AdMob & Polish/Publish).
```

---

## 🗂️ File-to-Syllabus Mapping Matrix

| Step # | Note File Name | Covered Syllabus Topics | Key Exam Highlight |
| :---: | :--- | :--- | :--- |
| **01** | [01_AsyncTask_and_AsyncTaskLoader.md](01_AsyncTask_and_AsyncTaskLoader.md) | Working in the background: Async Task and Async Task Loader | ANR error (>5s stall), 4 `AsyncTask` callbacks, Rotation Memory Leak flaws, `AsyncTaskLoader` + `LoaderManager.LoaderCallbacks` |
| **02** | [02_Connect_to_Internet_and_Broadcast_Receivers.md](02_Connect_to_Internet_and_Broadcast_Receivers.md) | Connect to the Internet, Broadcast Receivers | `INTERNET` permission, `HttpURLConnection` pattern, JSON parsing (`JSONObject`/`JSONArray`), Static vs Dynamic Registration comparison matrix, `LocalBroadcastManager` |
| **03** | [03_Services_Scheduling_and_Notifications.md](03_Services_Scheduling_and_Notifications.md) | Services, Scheduling and optimizing background tasks – Notifications | 3 Service Types (Foreground, Started, Bound with embedded `images/services.png`), `IntentService`, `JobScheduler` vs `AlarmManager`, `NotificationChannel` (API 26+), `PendingIntent` |
| **04** | [04_Scheduling_Alarms_and_Efficient_Data_Transfer.md](04_Scheduling_Alarms_and_Efficient_Data_Transfer.md) | Scheduling Alarms, and Transferring Data Efficiently | 4 Alarm Types (`ELAPSED_REALTIME` vs `RTC`), `setInexactRepeating()`, `FLAG_NO_CREATE` alarm check, Wireless Radio State Machine (Tail Time battery drain), Bundling & Prefetching |
| **05** | [04_Question_Paper_Solutions_Unit3.md](04_Question_Paper_Solutions_Unit3.md) | University Exam Question Paper Solutions (2020-21) | Implicit vs Explicit Receivers, Email sending Intent code, CountDownTimer complete app code, Activity parameter passing code |
| **QP 05** | [QP/05_Question_Paper_Solutions_Units3_4_5.md](QP/05_Question_Paper_Solutions_Units3_4_5.md) | Comprehensive Exam Solutions (Units 3, 4 & 5) | AsyncTask generics, Notification app code, Loaders, Data Transfer, SharedPreferences, Location Services, Alarms, SQLiteOpenHelper callbacks, Permissions, App Publishing steps |
| **QP 06** | [QP/06_Question_Paper_Solutions_Unit3_Background_PartA.md](QP/06_Question_Paper_Solutions_Unit3_Background_PartA.md) | Background Tasks & Security Paper Part-A Solutions | AsyncTask definition, LoaderManager, Broadcast types & examples, Intent Filter definition, SharedPreferences vs SavedInstanceState, Strong reference justification |
| **QP 07** | [QP/07_Question_Paper_Solutions_Unit3_Background_PartB.md](QP/07_Question_Paper_Solutions_Unit3_Background_PartB.md) | Background Tasks & Security Paper Part-B Solutions | App Publishing steps, AsyncTaskLoader diagram & code, Permission grant/revoke/types/best practices, BroadcastReceiver implementation steps, App Security practices |
| **QP 08** | [QP/08_Question_Paper_Solutions_Unit3_Broadcasts_Paper.md](QP/08_Question_Paper_Solutions_Unit3_Broadcasts_Paper.md) | Broadcasts, Services & Firebase Paper Solutions | Quiz 1-5, Explicit vs Implicit Intents code, Power Connection Broadcast Receiver Java & Manifest code, Services Lifecycle diagram & explanation, Firebase/AdMob & Polish/Publish |
