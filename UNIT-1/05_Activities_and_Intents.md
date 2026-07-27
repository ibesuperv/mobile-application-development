# Activities and Intents

---

## 1. What is an Activity? (Exam Core Definition)

An **Activity** is a fundamental application component in Android that provides a single screen with an interactive user interface (UI) through which users can perform actions (e.g., dial a phone number, take a photo, send an email, or view a map).

### Key Characteristics of Activities:
- **Independent Component**: Although activities work together to present a seamless UI, each activity is decoupled and operates independently. An application can launch activities inside other apps, and vice versa.
- **Main Activity Entry Point**: Every application designates one activity as the "Main" activity (`MainActivity`), presented when the user launches the app icon from the home screen launcher.
- **Managed Lifecycle & Back Stack**: Activities are created, paused, resumed, and destroyed by the Android OS framework, managed using an internal LIFO (Last-In, First-Out) stack structure called the **Back Stack**.

---

## 2. Anatomy of Activity Creation & Registration

To create and use an Activity in Android, **three steps are mandatory**:

```
+------------------------------------------------------------------+
| 1. Create Java Class extending AppCompatActivity                 |
+------------------------------------------------------------------+
                               |
                               v
+------------------------------------------------------------------+
| 2. Implement UI in XML Layout & Inflate via setContentView()    |
+------------------------------------------------------------------+
                               |
                               v
+------------------------------------------------------------------+
| 3. Declare Activity inside AndroidManifest.xml (<activity> tag)  |
+------------------------------------------------------------------+
```

### A. Java Class Declaration (`MainActivity.java`)
Activities extend `AppCompatActivity` (a subclass of `Activity` offering backward-compatible support for Material Design themes and Action Bar APIs across older Android versions).

```java
package com.example.android.twoactivities;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // Inflates the XML layout resource into the activity window
        setContentView(R.layout.activity_main);
    }
}
```

### B. Manifest Registration (`AndroidManifest.xml`)
Before the Android runtime can start an activity, **it must be declared inside `AndroidManifest.xml`**. Unregistered activities throw an `ActivityNotFoundException` at runtime.

```xml
<application ... >

    <!-- Main Activity (Launcher Entry Point) -->
    <activity android:name=".MainActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>

    <!-- Child / Second Activity -->
    <activity 
        android:name=".SecondActivity"
        android:label="@string/activity2_name"
        android:parentActivityName=".MainActivity">
        <!-- Backward-compatibility meta-data for API < 16 -->
        <meta-data
            android:name="android.support.PARENT_ACTIVITY"
            android:value="com.example.android.twoactivities.MainActivity" />
    </activity>

</application>
```

#### Manifest Activity Attributes:
- `android:name`: Specifies the Java class name (required).
- `android:label`: App bar title text for this activity.
- `android:parentActivityName`: Specifies the parent activity for **Up Navigation** (API 16+).
- `<meta-data>`: Provides backward-compatible Up navigation support for API levels < 16.

---

## 3. What is an Intent? (Exam Core Definition)

An **Intent** is an asynchronous messaging object used to request an action from another application component (such as an Activity, Service, or Broadcast Receiver).

### Primary Functions of Intents:
1. **Starting an Activity**: Passing an `Intent` object to `startActivity()` or `startActivityForResult()`.
2. **Passing Data Between Components**: Transporting primitive types, URIs, and complex serializable objects across activity boundaries.
3. **Starting Services & Broadcasting Events**: Triggering background services (`startService()`) or delivering system-wide broadcasts (`sendBroadcast()`).

---

## 4. Intent Types: Explicit vs. Implicit Intents (6-Mark Exam Comparison)

| Technical Feature | Explicit Intent | Implicit Intent |
| :--- | :--- | :--- |
| **Definition** | Specifies the target component by its exact, fully-qualified class name. | Does not specify a target class name; specifies a general action to perform. |
| **Component Resolution** | Target component is known beforehand within the same application. | Resolved dynamically by Android OS matching action, category, and data type against Intent Filters. |
| **Security & Isolation** | High security. Used exclusively to navigate between screens in your own app. | Used to request external app services (e.g., camera, web browser, dialer, map viewer). |
| **Code Construction** | `new Intent(Context, TargetClass.class)` | `new Intent(Intent.ACTION_VIEW, Uri.parse("http://..."))` |

---

## 5. Explicit Intents: Starting Activities & Passing Data

### Scenario A: Launching a Second Activity (`startActivity`)

```java
// Inside Sender Activity (MainActivity.java)
Intent intent = new Intent(this, SecondActivity.class);
startActivity(intent);
```

---

### Scenario B: Passing Data Using Intent Extras & Bundles

Data is transferred between activities using **Intent Extras** stored inside a `Bundle` object as key-value pairs.

#### 1. Sending Data (`MainActivity.java`)
Keys should be defined as `public static final String` constants prefixed with the package namespace to prevent key collisions across apps.

```java
public class MainActivity extends AppCompatActivity {

    // Define public constant key prefixed with package name
    public static final String EXTRA_MESSAGE = "com.example.android.twoactivities.extra.MESSAGE";
    public static final String EXTRA_COUNT = "com.example.android.twoactivities.extra.COUNT";

    private void launchSecondActivity() {
        Intent intent = new Intent(this, SecondActivity.class);
        
        // Passing primitive values via putExtra
        intent.putExtra(EXTRA_MESSAGE, "Hello from MainActivity!");
        intent.putExtra(EXTRA_COUNT, 42);
        
        startActivity(intent);
    }
}
```

#### 2. Receiving Data (`SecondActivity.java`)

```java
public class SecondActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        // Retrieve Intent that launched this activity
        Intent intent = getIntent();

        if (intent != null) {
            // Extract extras using matching public key constants
            String message = intent.getStringExtra(MainActivity.EXTRA_MESSAGE);
            int count = intent.getIntExtra(MainActivity.EXTRA_COUNT, 0); // 0 is fallback default

            TextView textView = (TextView) findViewById(R.id.text_message);
            textView.setText(message + " | Count: " + count);
        }
    }
}
```

---

## 6. Returning Results from an Activity (`startActivityForResult`)

When an activity needs to retrieve a return payload from a child activity (e.g., selecting a contact or entering text), it uses `startActivityForResult()`.

```
[MainActivity] --(startActivityForResult, RequestCode=1)--> [SecondActivity]
      ^                                                            |
      |                                                            v
[onActivityResult()] <--(setResult(RESULT_OK, replyIntent))--------+
```

### Complete Code Execution Flow:

#### Step 1: Launch Child Activity with Request Code (`MainActivity.java`)

```java
public class MainActivity extends AppCompatActivity {

    // Unique integer identifying the request
    public static final int TEXT_REQUEST = 1;

    public void launchReplyActivity(View view) {
        Intent intent = new Intent(this, SecondActivity.class);
        startActivityForResult(intent, TEXT_REQUEST);
    }
```

#### Step 2: Formulate & Return Result Payload (`SecondActivity.java`)

```java
public class SecondActivity extends AppCompatActivity {

    public static final String EXTRA_REPLY = "com.example.android.twoactivities.extra.REPLY";

    public void returnReply(View view) {
        EditText replyEditText = (EditText) findViewById(R.id.editText_second);
        String replyText = replyEditText.getText().toString();

        // Create a NEW intent object for return payload (do not reuse sending intent)
        Intent replyIntent = new Intent();
        replyIntent.putExtra(EXTRA_REPLY, replyText);

        // Set result code (RESULT_OK or RESULT_CANCELED) and return intent
        setResult(RESULT_OK, replyIntent);

        // Close SecondActivity and pop from back stack
        finish();
    }
}
```

#### Step 3: Handle Returned Data in Callback (`MainActivity.java`)

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        // Check which request code we are responding to
        if (requestCode == TEXT_REQUEST) {
            // Check if the child activity completed successfully
            if (resultCode == RESULT_OK && data != null) {
                String reply = data.getStringExtra(SecondActivity.EXTRA_REPLY);
                
                TextView replyTextView = (TextView) findViewById(R.id.text_header_reply);
                replyTextView.setText(reply);
                replyTextView.setVisibility(View.VISIBLE);
            }
        }
    }
}
```

---

## 7. Navigation Patterns: Back Navigation vs. Up Navigation (Exam Core Question)

```
        BACK NAVIGATION                           UP NAVIGATION
 (System-wide Back Button)                  (App Bar Left Arrow)
  Follows temporal Back Stack               Follows logical hierarchy
  History order across apps                 Defined inside Manifest
```

| Navigation Aspect | Back Navigation (Back Button) | Up Navigation (Up Button / Left Arrow) |
| :--- | :--- | :--- |
| **UI Location** | Physical/virtual device navigation bar at bottom of screen. | Left-facing arrow inside the top App Bar / Action Bar. |
| **Behavior Focus** | **Temporal History**: Navigates backwards through screen execution history (LIFO Back Stack). | **Hierarchical Ancestry**: Navigates up through the logical parent-child screen hierarchy. |
| **Cross-App Boundary** | Can exit current app and return to previous application or launcher. | Remains strictly contained within the current application hierarchy. |
| **Implementation** | Handled automatically by Android OS Back Stack (`onBackPressed()`). | Declared in Manifest using `android:parentActivityName` attribute. |

---

## 8. Navigating Up Implementation Example

To implement the Up button arrow in a child activity:

1. **Manifest Declaration**:
```xml
<activity 
    android:name=".SecondActivity"
    android:parentActivityName=".MainActivity">
    <meta-data
        android:name="android.support.PARENT_ACTIVITY"
        android:value="com.example.android.twoactivities.MainActivity" />
</activity>
```

2. **Java Action Bar Enable Code (`SecondActivity.java`)**:
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_second);

    // Enable Up arrow button in App Bar
    if (getSupportActionBar() != null) {
        getSupportActionBar().setDisplayHomeAsUpEnabled(true);
    }
}
```
