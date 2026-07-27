# Question Paper Solutions: Paper-5 Part-A (Questions 2 to 5 & Quiz)

---

## Question 2: Handling Click Events in Android App (2 Techniques with Code) (10 Marks)

Android provides two primary techniques to handle button tap/click events:

### Technique 1: Programmatic Listener Registration (`setOnClickListener`)
In Java code, instantiate the Button view using `findViewById()` and attach an anonymous `View.OnClickListener` interface (or lambda expression).

```java
// Technique 1: Programmatic setOnClickListener
Button btnSubmit = findViewById(R.id.btn_submit);

btnSubmit.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Toast.makeText(MainActivity.this, "Button Clicked via Listener!", Toast.LENGTH_SHORT).show();
    }
});

// Or using Java 8 Lambda expression:
// btnSubmit.setOnClickListener(v -> Toast.makeText(this, "Clicked!", Toast.LENGTH_SHORT).show());
```

---

### Technique 2: Declarative XML Attribute (`android:onClick`)
Declare the `android:onClick` attribute directly in the XML layout widget and define a matching public void method taking a `View` parameter in the host Activity.

#### 1. XML Layout Declaration (`activity_main.xml`):
```xml
<Button
    android:id="@+id/btn_save"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="SAVE"
    android:onClick="handleSaveButtonClick" />
```

#### 2. Java Host Activity Handler (`MainActivity.java`):
```java
// Method signature MUST be: public void <method_name>(View view)
public void handleSaveButtonClick(View view) {
    Toast.makeText(this, "Button Clicked via XML Handler!", Toast.LENGTH_SHORT).show();
}
```

---

## Question 3a: Service Life Cycle, When to Use & Started vs Bound Services (6 Marks)

### A. When to Use a Service (1.5 Marks)
Use a **Service** when an app needs to execute long-running background operations without displaying a user interface (e.g. streaming audio in the background, downloading files, syncing location).

---

### B. Service Life Cycle Diagram (2 Marks)

```
       STARTED SERVICE LIFECYCLE                   BOUND SERVICE LIFECYCLE
       +-----------------------+                   +---------------------+
       |    startService()     |                   |    bindService()    |
       +-----------+-----------+                   +----------+----------+
                   │                                          │
                   v                                          v
               onCreate()                                 onCreate()
                   │                                          │
            onStartCommand()                               onBind()
                   │                                          │
           [ Service Running ]                     [ Client Bound to Service ]
                   │                                          │
            stopSelf() / stopService()                   unbindService()
                   │                                          │
                   v                                          v
              onDestroy()                                 onUnbind() ──> onDestroy()
```

---

### C. Started Services vs Bound Services Comparison Matrix (2.5 Marks)

| Dimension | Started Service (`startService()`) | Bound Service (`bindService()`) |
| :--- | :--- | :--- |
| **Trigger Method** | Initiated by calling `startService(intent)`. | Initiated by calling `bindService(intent, connection, flags)`. |
| **Lifecycle** | Operates **indefinitely** in background until `stopSelf()` or `stopService()` is called. | Operates ONLY as long as client components are bound to it. |
| **Interaction / IPC** | Does NOT return results to caller; no direct client-server interface. | Returns an **`IBinder`** interface allowing client-server IPC. |
| **Stopping Condition** | Stops when `stopSelf()` or `stopService()` is invoked. | Destroys automatically when all clients unbind (`unbindService()`). |

---

## Question 3b: Send Queries to SQLite Database Code (4 Marks)

Sending `SELECT` queries to SQLite database using **`SQLiteDatabase.query()`** and traversing the **`Cursor`**.

### Database Query Implementation (`DatabaseAdapter.java`):

```java
// Table: "students" with columns: "_id", "name", "usn"
public void fetchAllStudents(SQLiteDatabase db) {
    String[] projection = {"_id", "name", "usn"}; // Columns to return
    String selection = "usn = ?";               // WHERE clause
    String[] selectionArgs = {"1AB21CS001"};     // WHERE argument (parameterized to prevent SQL injection)

    Cursor cursor = db.query(
            "students",       // Table Name
            projection,       // Target Columns
            selection,        // WHERE Clause
            selectionArgs,    // WHERE Arguments
            null,             // GROUP BY
            null,             // HAVING
            "name ASC"        // ORDER BY
    );

    if (cursor != null && cursor.moveToFirst()) {
        do {
            int id = cursor.getInt(cursor.getColumnIndexOrThrow("_id"));
            String name = cursor.getString(cursor.getColumnIndexOrThrow("name"));
            String usn = cursor.getString(cursor.getColumnIndexOrThrow("usn"));

            Log.d("SQLiteQuery", "ID: " + id + ", Name: " + name + ", USN: " + usn);
        } while (cursor.moveToNext());

        cursor.close(); // Mandatory to close cursor to prevent memory leaks!
    }
}
```

---

## Question 4a: Storage Options Matrix & Factors in Choosing Storage Option (7 Marks)

### A. 6 Persistent Storage Options in Android (4 Marks)

1. **Shared Preferences**: Key-value pairs for primitive data types stored in private XML files.
2. **Internal Storage**: Private flat files stored on device flash memory (`/data/data/<pkg>/files/`).
3. **External Storage**: Shared public file system for media files (Photos, Downloads) accessible via Scoped Storage.
4. **SQLite Database**: Embedded relational database for structured, tabular data relationships.
5. **Room Persistence Library**: Abstraction layer over SQLite providing compile-time SQL verification and object mapping.
6. **Network / Cloud Storage**: Remote cloud servers (Firebase, AWS, REST APIs) for server-side persistence.

---

### B. Factors to Consider in Choosing Storage Option (3 Marks)
- **Data Structure**: Key-value pairs (`SharedPreferences`) vs structured tabular data (`SQLite`/`Room`) vs raw media files (`External Storage`).
- **Data Privacy**: Private to app (`Internal Storage`) vs shared across external apps (`Scoped External Storage`).
- **Data Lifetime**: Transient session (`SavedInstanceState`) vs permanent application life (`SQLite`/`Preferences`).
- **Network Dependency**: Local offline access (`SQLite`) vs cloud backup (`Firebase`).

---

## Question 4b: Facts About Internal vs External Storage Spaces (3 Marks)

| Facts | Internal Storage | External Storage |
| :--- | :--- | :--- |
| **Accessibility / Security** | Strictly **private** to your app (`/data/data/<pkg>/`); inaccessible to other apps or user. | **Public/Shared** file space; accessible to user via file manager or companion apps. |
| **Uninstallation Behavior** | Files are **automatically deleted** completely when user uninstalls the app. | Files in shared media directories (Photos/Downloads) **remain on device** after uninstall. |
| **Availability Guarantee** | Always guaranteed to be available on device internal flash memory. | May be unmounted, removable (SD card), or restricted by Scoped Storage APIs. |

---

## Question 5: Discuss Fragments and Room Database with Examples (10 Marks)

### A. Fragments (5 Marks)
A **Fragment** represents a reusable, modular portion of an Activity's user interface with its own layout and lifecycle. Multiple fragments can be combined in a single Activity to build multi-pane dynamic tablet UIs.

```java
// Fragment Example Subclass
public class ExampleFragment extends Fragment {

    public ExampleFragment() {
        // Required empty public constructor
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        // Inflate the XML layout for this fragment
        View view = inflater.inflate(R.layout.fragment_example, container, false);
        Button btn = view.findViewById(R.id.btn_frag);
        btn.setOnClickListener(v -> Toast.makeText(getContext(), "Fragment Clicked!", Toast.LENGTH_SHORT).show());
        return view;
    }
}
```

---

### B. Room Database (5 Marks)
**Room** is an AndroidX Object Mapping library that provides an abstraction layer over SQLite, offering compile-time SQL query verification and clean Java/Kotlin annotations.

#### 3 Core Components of Room:
1. **Entity (`@Entity`)**: Represents a database table schema class.
2. **DAO (`@Dao`)**: Contains SQL query methods (`@Insert`, `@Query`, `@Delete`).
3. **Database (`@Database`)**: Main database holder extending `RoomDatabase`.

```java
// 1. Room Entity
@Entity(tableName = "users")
public class User {
    @PrimaryKey(autoGenerate = true)
    public int id;
    public String name;
}

// 2. Room Data Access Object (DAO)
@Dao
public interface UserDao {
    @Insert
    void insertUser(User user);

    @Query("SELECT * FROM users")
    List<User> getAllUsers();
}
```

---

## Question Paper 5 Set B: Platform Challenges, DVM vs JVM, Manifest, Debugger, Lifecycle & Stack (10 Marks Each)

### 1a. Platform Challenges with Examples (6 Marks):
Detailed in [15_Question_Paper_Solutions_Extra.md](file:///v:/ACADEMIC/6TH%20SEM/MOBILE/NOTES/UNIT-1/15_Question_Paper_Solutions_Extra.md#question-1-android-platform-challenges-with-detailed-examples-10-marks).

### 1b. DVM vs. JVM Difference (4 Marks):
- **DVM**: Register-based architecture, compiles `.class` files into a single compressed `classes.dex`, optimized for low-RAM mobile CPUs.
- **JVM**: Stack-based architecture, executes individual `.class` files, designed for desktop/server CPUs.

---

### 2a. Justify Need & Basic Structure of `AndroidManifest.xml` (5 Marks):
Detailed in [11_Question_Paper_Solutions_Part4.md](file:///v:/ACADEMIC/6TH%20SEM/MOBILE/NOTES/UNIT-1/11_Question_Paper_Solutions_Part4.md#question-2-2020-21-need--structure-of-androidmanifestxml--res-directory-10-marks).

### 2b. Demonstrate Android Studio Debugger (5 Marks):
- **Breakpoints**: Red dot set on code lines to pause execution.
- **Stepping Controls**:
  - `Step Over (F8)`: Executes current line and moves to next line.
  - `Step Into (F7)`: Steps inside the called method.
  - `Step Out (Shift + F8)`: Finishes current method and returns to caller.
  - `Resume Program (F9)`: Resumes execution until next breakpoint.
- **Watches & Variables Pane**: Inspects live variable values in RAM memory during execution.

---

### 3. Lifecycle Methods of Android Activity with Diagram (10 Marks Java Code):
Detailed in [10_Question_Paper_Solutions_Unit1_Part2.md](file:///v:/ACADEMIC/6TH%20SEM/MOBILE/NOTES/UNIT-1/10_Question_Paper_Solutions_Unit1_Part2.md#question-17-explain-android-activity-life-cycle-10-marks).

### 4. Android Software Stack Block Diagram (10 Marks):
Detailed in [11_Question_Paper_Solutions_Part4.md](file:///v:/ACADEMIC/6TH%20SEM/MOBILE/NOTES/UNIT-1/11_Question_Paper_Solutions_Part4.md#question-20-explain-the-layered-architecture-of-android-os-10-marks).

### 5. Intents & Categories Demonstration (10 Marks):
Detailed in [15_Question_Paper_Solutions_Extra.md](file:///v:/ACADEMIC/6TH%20SEM/MOBILE/NOTES/UNIT-1/15_Question_Paper_Solutions_Extra.md#question-2b-purpose-of-intents--categories-with-case-study-5-marks).

---

## Quiz Answers (Questions 1 to 6)

1. **State the full form of APK and mention its importance**:
   - **Full Form**: Android Package (`.apk`).
   - **Importance**: The compiled ZIP file format containing DEX bytecode, binary manifest, and resources used by Android OS to distribute and install apps.
2. **Edit the ________ file to add features components and permissions**:
   - **Answer**: **`AndroidManifest.xml`**
3. **In Android Studio illustrate how to create a virtual device (emulator)**:
   - **Answer**: Tools > Device Manager > Create Virtual Device > Select Hardware Profile (Pixel 6) > Select & Download System Image (API 33) > Finish & Run (`Shift + F10`).
4. **Mention the latest release of Android version**:
   - **Answer**: **Android 14** (API Level 34 / Code name: *Upside Down Cake*).
5. **Categorize the contents of the `res` directory in the Android project structure**:
   - **Answer**: `layout`, `drawable`, `values` (`strings.xml`, `colors.xml`), `mipmap` (icons), `menu`, `raw`.
6. **While developing Android application developers can test their apps on ------------ Give various options**:
   - **Options**:
     1. **Android Virtual Device (AVD Emulator)** in Android Studio.
     2. **Physical Android Device** connected via USB Debugging.
     3. **Wireless Debugging** over Wi-Fi (API 30+).
     4. **Cloud Test Labs** (Firebase Test Lab).
