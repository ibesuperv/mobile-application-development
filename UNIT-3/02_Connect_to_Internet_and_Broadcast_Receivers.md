# Connect to the Internet & Broadcast Receivers

---

## SECTION 1: Connect to the Internet & Networking

---

### 1. Mandatory Manifest Permissions & Security Best Practices

Before an app can execute network calls, required permissions MUST be declared in `AndroidManifest.xml`:

```xml
<!-- Required for making HTTP/HTTPS network connections -->
<uses-permission android:name="android.permission.INTERNET" />

<!-- Required for checking active network connectivity states (Wi-Fi vs Mobile Data) -->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

#### Network Security Guidelines:
- **HTTPS Enforcement**: Use `HttpsURLConnection` instead of unencrypted `HttpURLConnection` for all sensitive traffic to prevent Man-in-the-Middle (MitM) attacks on public Wi-Fi hotspots.
- **Do NOT run on Main Thread**: Executing network transactions on the Main Thread throws a `NetworkOnMainThreadException`. Always run networking in background worker threads (`AsyncTask`, `AsyncTaskLoader`, or `Executor`).

---

### 2. Building Request URIs (`Uri.Builder`)

Construct request URLs programmatically using `Uri.parse()`, `buildUpon()`, and `appendQueryParameter()`:

```java
// Target URL: https://www.googleapis.com/books/v1/volumes?q=pride+prejudice&maxResults=5&printType=books
final String BOOK_BASE_URL = "https://www.googleapis.com/books/v1/volumes";

Uri builtURI = Uri.parse(BOOK_BASE_URL).buildUpon()
    .appendQueryParameter("q", "pride+prejudice")
    .appendQueryParameter("maxResults", "5")
    .appendQueryParameter("printType", "books")
    .build();

URL requestUrl = new URL(builtURI.toString());
```

---

### 3. Making HTTP Connections (`HttpURLConnection` Pattern)

```java
public String downloadUrlData(String urlString) throws IOException {
    InputStream inputStream = null;
    HttpURLConnection urlConnection = null;

    try {
        URL url = new URL(urlString);
        urlConnection = (HttpURLConnection) url.openConnection();
        
        // Configure timeouts and request method
        urlConnection.setReadTimeout(10000 /* milliseconds */);
        urlConnection.setConnectTimeout(15000 /* milliseconds */);
        urlConnection.setRequestMethod("GET");
        urlConnection.setDoInput(true);

        // Open connection and check HTTP response code (200 OK)
        urlConnection.connect();
        int responseCode = urlConnection.getResponseCode();
        if (responseCode == HttpURLConnection.HTTP_OK) {
            inputStream = urlConnection.getInputStream();
            return convertStreamToString(inputStream);
        } else {
            return null;
        }
    } finally {
        // Always disconnect and close streams in finally block
        if (urlConnection != null) {
            urlConnection.disconnect();
        }
        if (inputStream != null) {
            inputStream.close();
        }
    }
}

// Convert InputStream to String using BufferedReader
private String convertStreamToString(InputStream stream) throws IOException {
    BufferedReader reader = new BufferedReader(new InputStreamReader(stream, "UTF-8"));
    StringBuilder sb = new StringBuilder();
    String line;
    while ((line = reader.readLine()) != null) {
        sb.append(line);
    }
    return sb.toString();
}
```

---

### 4. Parsing JSON Responses (`JSONObject` & `JSONArray`)

Web APIs return JSON structures. Use `JSONObject` and `JSONArray` to extract target data fields:

```java
// Sample JSON: {"items": [{"volumeInfo": {"title": "Pride and Prejudice"}}]}
String jsonResponse = "..."; // JSON string from network

try {
    JSONObject rootObject = new JSONObject(jsonResponse);
    JSONArray itemsArray = rootObject.getJSONArray("items");

    for (int i = 0; i < itemsArray.length(); i++) {
        JSONObject book = itemsArray.getJSONObject(i);
        JSONObject volumeInfo = book.getJSONObject("volumeInfo");
        String title = volumeInfo.getString("title");
        Log.d("BookTitle", "Title: " + title);
    }
} catch (JSONException e) {
    e.printStackTrace();
}
```

---

### 5. Managing Network Connectivity State (`ConnectivityManager`)

Check network status prior to initiating network calls to avoid failures:

```java
ConnectivityManager connMgr = (ConnectivityManager) 
        getSystemService(Context.CONNECTIVITY_SERVICE);

NetworkInfo activeNetwork = connMgr.getActiveNetworkInfo();
boolean isConnected = activeNetwork != null && activeNetwork.isConnected();

if (isConnected) {
    boolean isWiFi = activeNetwork.getType() == ConnectivityManager.TYPE_WIFI;
    boolean isMobile = activeNetwork.getType() == ConnectivityManager.TYPE_MOBILE;
    // Safe to initiate background network call
} else {
    // Show offline message to user
}
```

---

## SECTION 2: Broadcast Receivers

A **Broadcast Receiver** (`BroadcastReceiver`) is an Android core component that listens for system-wide or app-wide broadcast announcements (Intents).

```
                      BROADCAST INTENTS SYSTEM
                                 |
         +-----------------------+-----------------------+
         |                                               |
         v                                               v
SYSTEM BROADCASTS                               CUSTOM BROADCASTS
(Sent by Android OS)                            (Sent by user apps)
- BOOT_COMPLETED                                - ACTION_CUSTOM_BROADCAST
- ACTION_POWER_CONNECTED
- ACTION_POWER_DISCONNECTED
         |                                               |
         +-----------------------+-----------------------+
                                 |
                                 v
                         BROADCAST RECEIVER
                       (Executes onReceive())
```

---

### 1. Types of Broadcast Intents

1. **System Broadcasts**: Sent automatically by the OS when system events occur (e.g., `Intent.ACTION_BOOT_COMPLETED`, `Intent.ACTION_POWER_CONNECTED`, `Intent.ACTION_POWER_DISCONNECTED`, `Intent.ACTION_BATTERY_LOW`).
2. **Custom Broadcasts**: Created and dispatched by your app using `sendBroadcast(intent)` to notify other components/apps of events (e.g., data finished downloading).

---

### 2. Static vs. Dynamic Receiver Registration (Exam 6-Mark Core Question)

| Dimension | Static Registration | Dynamic Registration |
| :--- | :--- | :--- |
| **Location** | Declared in `AndroidManifest.xml` inside `<receiver>` tag. | Declared programmatically in Java using `registerReceiver()`. |
| **App Execution Lifecycle** | **Works even if app is NOT running**. OS launches app process automatically when broadcast arrives. | **Tied to Activity Lifecycle**. Only receives broadcasts while registered (e.g. between `onResume` and `onPause`). |
| **Best Use Cases** | `BOOT_COMPLETED`, device power state triggers. | UI updates that only matter when screen is actively visible to user. |
| **System Restrictions** | Modern Android (API 26+) restricts static registration for most implicit broadcasts to save battery/RAM. | Preferred modern approach for non-boot system broadcasts. |

---

### 3. Implementation Patterns

#### A. Creating a `BroadcastReceiver` Subclass:
```java
public class CustomReceiver extends BroadcastReceiver {

    private static final String ACTION_CUSTOM_BROADCAST = 
            "com.example.android.ACTION_CUSTOM_BROADCAST";

    @Override
    public void onReceive(Context context, Intent intent) {
        String intentAction = intent.getAction();
        if (intentAction != null) {
            switch (intentAction) {
                case Intent.ACTION_POWER_CONNECTED:
                    Toast.makeText(context, "Power Connected!", Toast.LENGTH_SHORT).show();
                    break;
                case Intent.ACTION_POWER_DISCONNECTED:
                    Toast.makeText(context, "Power Disconnected!", Toast.LENGTH_SHORT).show();
                    break;
                case ACTION_CUSTOM_BROADCAST:
                    Toast.makeText(context, "Custom Broadcast Received!", Toast.LENGTH_SHORT).show();
                    break;
            }
        }
    }
}
```

---

#### B. Static Registration (`AndroidManifest.xml`):
```xml
<receiver
    android:name=".CustomReceiver"
    android:exported="false">
    <intent-filter>
        <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
        <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED" />
    </intent-filter>
</receiver>
```

---

#### C. Dynamic Registration (`MainActivity.java`):
```java
public class MainActivity extends AppCompatActivity {

    private CustomReceiver mReceiver = new CustomReceiver();

    @Override
    protected void onResume() {
        super.onResume();
        // Register receiver dynamically for power events
        IntentFilter filter = new IntentFilter();
        filter.addAction(Intent.ACTION_POWER_CONNECTED);
        filter.addAction(Intent.ACTION_POWER_DISCONNECTED);
        this.registerReceiver(mReceiver, filter);
    }

    @Override
    protected void onPause() {
        super.onPause();
        // ALWAYS unregister dynamic receiver to prevent memory leaks
        this.unregisterReceiver(mReceiver);
    }
}
```

---

### 4. `LocalBroadcastManager` (Exam 4-Mark Concept)

When broadcasts are sent via normal `sendBroadcast()`, any application on the device can receive or forge those intents, posing a security vulnerability.

- **`LocalBroadcastManager`**: Restricts broadcast intents strictly to **within the single application process**.
- **Advantages**:
  1. **Security**: Broadcast data doesn't leave the app; external apps cannot intercept or inject fake broadcasts.
  2. **Efficiency**: Does not require cross-process communication (IPC) overhead, making it significantly faster.
  3. Must be registered dynamically using `LocalBroadcastManager.getInstance(this).registerReceiver()`.

```java
// 1. Send Local Broadcast:
Intent customIntent = new Intent("com.example.ACTION_CUSTOM_BROADCAST");
LocalBroadcastManager.getInstance(this).sendBroadcast(customIntent);

// 2. Register Local Receiver:
LocalBroadcastManager.getInstance(this).registerReceiver(
    mReceiver, 
    new IntentFilter("com.example.ACTION_CUSTOM_BROADCAST")
);

// 3. Unregister Local Receiver:
LocalBroadcastManager.getInstance(this).unregisterReceiver(mReceiver);
```
