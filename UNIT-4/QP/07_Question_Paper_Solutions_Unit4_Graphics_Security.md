# Question Paper Solutions: Drawing, Web, Animations, Security & Firebase (Questions 1 to 5)

---

## Question 1 (Paper 1): 5 Methods Used in Drawing Features in Android (10 Marks)

Custom 2D graphics drawing in Android is accomplished by overriding **`onDraw(Canvas canvas)`** inside a custom `View` class using **`Canvas`** and **`Paint`** objects.

```
                           CANVAS DRAWING ARCHITECTURE
                                        │
                         CustomView extends View
                                        │
                               onDraw(Canvas canvas)
                                        │
     +------------+------------+--------+-------+------------+
     |            |            |                |            |
     v            v            v                v            v
drawColor()   drawRect()   drawCircle()    drawLine()   drawText()
```

### 5 Canvas Drawing Methods Explained:

1. **`drawColor(int color)`**:
   - Fills the entire canvas background with a uniform solid color (e.g. `canvas.drawColor(Color.WHITE)`).
2. **`drawRect(float left, float top, float right, float bottom, Paint paint)`**:
   - Draws a rectangle specified by boundary coordinates using the styling attributes defined in the `Paint` object.
3. **`drawCircle(float cx, float cy, float radius, Paint paint)`**:
   - Draws a circle centered at coordinate $(cx, cy)$ with a specified radius.
4. **`drawLine(float startX, float startY, float stopX, float stopY, Paint paint)`**:
   - Draws a straight line segment between starting point $(startX, startY)$ and endpoint $(stopX, stopY)$.
5. **`drawText(String text, float x, float y, Paint paint)`**:
   - Renders a text string at coordinate $(x, y)$ using the font size, typeface, and color configured in `Paint`.

```java
// Custom View Drawing Example
public class MyCanvasView extends View {
    private Paint mPaint = new Paint();

    public MyCanvasView(Context context) { super(context); }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawColor(Color.LIGHTGRAY); // 1. drawColor
        
        mPaint.setColor(Color.BLUE);
        canvas.drawRect(50, 50, 200, 200, mPaint); // 2. drawRect
        
        mPaint.setColor(Color.RED);
        canvas.drawCircle(300, 300, 80, mPaint); // 3. drawCircle
        
        mPaint.setColor(Color.BLACK);
        canvas.drawLine(0, 400, 500, 400, mPaint); // 4. drawLine
        
        mPaint.setTextSize(40);
        canvas.drawText("Canvas Graphics", 50, 500, mPaint); // 5. drawText
    }
}
```

---

## Question 2 (Paper 1): Role of Internet & Web Pages Connected to Android Apps (10 Marks)

### A. Role of Internet in Android App Usage (5 Marks)
1. **Dynamic Content Fetching**: Enables apps to fetch real-time server data (news feeds, weather updates, social posts) via RESTful APIs using HTTP clients (`HttpURLConnection`).
2. **Cloud Synchronization**: Syncs local SQLite database state with remote cloud servers (e.g., Google Drive sync).
3. **Authentication & Payments**: Facilitates OAuth login (Google Sign-In, Firebase Auth) and secure payment gateway processing.

---

### B. What are Web Pages & How Connected to Android Apps (5 Marks)
- **Web Pages**: HTML, CSS, and JavaScript documents hosted on web servers accessible via URLs.
- **Connection Mechanisms to Android Apps**:
  1. **`WebView` Widget**: Embeds an active web browser engine inside an Activity layout (`webView.loadUrl("https://example.com")`).
  2. **Chrome Custom Tabs**: Opens an optimized, customized Chrome browser tab within the app for secure OAuth authentication.
  3. **REST APIs & JSON**: App communicates with backend web servers by making HTTP requests and parsing JSON responses.

---

## Question 3 (Paper 1) / Question 3 (Set A): Permissions in Android (Grant/Revoke, Types & Best Practices) (10 Marks)

### i. Grant and Revoke Mechanisms:
- **Install-Time**: Normal permissions declared in `AndroidManifest.xml` are auto-granted during installation.
- **Runtime Prompt (API 23+)**: Dangerous permissions require runtime prompts using `ActivityCompat.requestPermissions()`.
- **Revocation**: Users can revoke granted permissions anytime via **Settings > Apps > Permissions**.

### ii. Types of Permissions:
- **Normal Permissions**: Low-risk permissions (e.g. `INTERNET`, `VIBRATE`) granted automatically.
- **Dangerous Permissions**: Accesses private user data or sensitive hardware (e.g. `CAMERA`, `READ_CONTACTS`, `LOCATION`, `SEND_SMS`). Requires user runtime dialog approval.
- **Signature Permissions**: Granted automatically if the requesting app is signed with the same digital certificate as the declaring app.

### iii. Best Practices of Permissions:
- Request minimum required permissions.
- Use system implicit intents (e.g., `ACTION_IMAGE_CAPTURE`) instead of direct hardware permissions.
- Provide clear context rationale before requesting runtime permissions.

---

## Question 4 (Paper 1 & Paper 2): Best Practices for Security (10 Marks)

1. **Use `EncryptedSharedPreferences` & KeyStore**: Encrypt sensitive strings (tokens, API keys) using AES-256 keys managed by Android KeyStore.
2. **Enforce HTTPS Network Security Configuration**: Block cleartext HTTP traffic in `res/xml/network_security_config.xml` to prevent Man-in-the-Middle (MitM) attacks.
3. **Enable R8 / ProGuard Obfuscation**: Set `minifyEnabled true` in `build.gradle` to shrink, obfuscate, and protect DEX bytecode against decompilation.
4. **Isolate Internal Components (`android:exported="false"`)**: Set `android:exported="false"` on internal Activities, Services, and Receivers to block unauthorized external app access.
5. **Enforce Private File Storage (`MODE_PRIVATE`)**: Store application data exclusively in internal private memory (`/data/data/<pkg>/`).
6. **SQL Injection Defense**: Use parameterized SQL queries (`db.query()` with `selectionArgs`) instead of concatenating strings in raw SQL queries.

---

## Question 5 (Paper 1 & Paper 2): What is Firebase? Firebase Realtime Database with Example (10 Marks)

### A. What is Firebase? (3 Marks)
**Firebase** is a Backend-as-a-Service (BaaS) platform developed by Google that provides cloud services including authentication, real-time NoSQL databases, cloud messaging (FCM), crash reporting, and analytics.

---

### B. Firebase Realtime Database & Executable Example (7 Marks)
The **Firebase Realtime Database** is a cloud-hosted NoSQL database where data is stored as JSON objects and synchronized in **real-time** across all connected mobile clients.

```java
// Firebase Realtime Database Read/Write Example
public class FirebaseActivity extends AppCompatActivity {

    private DatabaseReference mDatabase;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 1. Get reference to root JSON tree node "users"
        mDatabase = FirebaseDatabase.getInstance().getReference("users");

        // 2. WRITE Operation: Add user object under ID "user_101"
        User user = new User("John Doe", "john@gmail.com");
        mDatabase.child("user_101").setValue(user);

        // 3. READ Operation: Listen for real-time changes
        mDatabase.child("user_101").addValueEventListener(new ValueEventListener() {
            @Override
            public void onDataChange(@NonNull DataSnapshot snapshot) {
                User updatedUser = snapshot.getValue(User.class);
                if (updatedUser != null) {
                    Log.d("FirebaseDB", "User Name: " + updatedUser.name);
                }
            }

            @Override
            public void onCancelled(@NonNull DatabaseError error) {
                Log.e("FirebaseDB", "Failed to read value", error.toException());
            }
        });
    }
}
```

---

## Question 1 (Paper 2): 4 Animation Components in Android (10 Marks)

1. **Animate Bitmaps (`BitmapDrawable` / `AnimatedVectorDrawable`)**:
   - Animates frame-by-frame image sequences or vector paths (e.g. morphing a play icon into a pause icon using `AnimatedVectorDrawable`).
2. **Animate UI Visibility and Motion (`ViewPropertyAnimator`)**:
   - Smoothly animates view properties like opacity (`alpha`), translation (`translationX`), and scale (`scaleX`) using `view.animate().alpha(0.0f).setDuration(300)`.
3. **Animate Layout Changes (`LayoutTransition` / `TransitionManager`)**:
   - Automatically animates container layouts when child views are added, removed, or resized using `android:animateLayoutChanges="true"` or `TransitionManager.beginDelayedTransition()`.
4. **Animate Between Activities (`ActivityOptions` / `overridePendingTransition`)**:
   - Applies custom slide or fade transition animations when navigating between Activity screens:
     ```java
     startActivity(intent);
     overridePendingTransition(R.anim.slide_in_right, R.anim.slide_out_left);
     ```

---

## Question 2 (Paper 2): Demonstrate Sending SMS in Android (Methods & Code) (10 Marks)

Sending SMS in Android can be accomplished via **`SmsManager`** (direct background sending) or **`Intent.ACTION_SENDTO`** (opening the default SMS app).

### Method 1: Direct SMS Sending using `SmsManager`

#### 1. Manifest Permission (`AndroidManifest.xml`):
```xml
<uses-permission android:name="android.permission.SEND_SMS" />
```

#### 2. Java Code Implementation (`MainActivity.java`):
```java
public void sendSMS(String phoneNumber, String message) {
    // Runtime Permission Check
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.SEND_SMS) 
            == PackageManager.PERMISSION_GRANTED) {

        // Get default SmsManager instance
        SmsManager smsManager = SmsManager.getDefault();

        // sendTextMessage(destinationAddress, scAddress, text, sentIntent, deliveryIntent)
        smsManager.sendTextMessage(phoneNumber, null, message, null, null);
        Toast.makeText(this, "SMS Sent Successfully!", Toast.LENGTH_SHORT).show();
    } else {
        ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.SEND_SMS}, 1);
    }
}
```

---

## Question 3 (Paper 2): Connect, Manage, Optimize and Read Network Usage in Android (10 Marks)

1. **Connecting to Network**:
   - Check network availability using **`ConnectivityManager`** and execute HTTP requests off the main thread using **`HttpURLConnection`**.
2. **Managing Network State**:
   - Listen for active network connection changes (Wi-Fi vs Cellular) using `ConnectivityManager.NetworkCallback`.
3. **Optimizing Network Usage**:
   - **Data Bundling**: Combine multiple small network transfers into a single transmission window.
   - **Prefetching**: Download upcoming content in single bursts on unmetered Wi-Fi connections to eliminate radio Tail Time battery drain.
4. **Reading Network Usage**:
   - Use **`TrafficStats`** or **`NetworkStatsManager`** APIs to query transmitted and received data byte counters (`TrafficStats.getUidRxBytes(uid)` and `TrafficStats.getUidTxBytes(uid)`).
