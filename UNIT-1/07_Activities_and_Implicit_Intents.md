# Activities and Implicit Intents

---

## 1. Introduction to Implicit Intents (Exam Core Concept)

Unlike an **Explicit Intent** (which specifies the target component by its exact Java class name), an **Implicit Intent** declares a general action to perform without naming a specific target activity or application component.

### How Implicit Intents Work:
When an app sends an implicit intent, the Android operating system checks all installed applications on the device, comparing the requested action against **Intent Filters** declared in their `AndroidManifest.xml` files.
- **Single Match**: If only one app component matches, Android launches it immediately.
- **Multiple Matches**: If multiple apps can handle the request (e.g., multiple web browsers or photo viewers), Android presents an **App Chooser Dialog** allowing the user to select their preferred app.
- **Zero Matches**: If no app matches the request, calling `startActivity()` causes an unhandled `ActivityNotFoundException` and crashes the app.

---

## 2. Fields of an Implicit Intent

Implicit intents rely on three main fields to describe the requested operation:

1. **Intent Action (`ACTION_`)**:
   - The generic action the receiving activity should perform.
   - Defined as constants in the `Intent` class starting with `ACTION_`.
   - Common Actions:
     - `Intent.ACTION_VIEW`: View data (webpage URL, map location, photo).
     - `Intent.ACTION_SEND`: Share data/text with external apps (email, chat, social media).
     - `Intent.ACTION_DIAL`: Open phone dialer with a specific number.
     - `Intent.ACTION_EDIT`: Edit data.

2. **Intent Data & MIME Type**:
   - **Data URI**: Specifies the location of the data to operate on as a `Uri` object (e.g., `http://`, `tel:`, `geo:`, `content://`).
   - **MIME Type**: Defines the explicit data format (e.g., `"text/plain"`, `"image/jpeg"`, `"video/mp4"`). Defined using `setType()`.

3. **Intent Category (`CATEGORY_`)**:
   - Optional metadata categorizing the target component type.
   - Common Categories:
     - `Intent.CATEGORY_DEFAULT`: Mandatory for activities accepting implicit intents.
     - `Intent.CATEGORY_BROWSABLE`: Activity can safely be invoked by a web browser link.
     - `Intent.CATEGORY_LAUNCHER`: Top-level app launcher icon on the device home screen.

---

## 3. Sending Implicit Intents Safely (Exam Defensive Pattern)

To prevent runtime crashes (`ActivityNotFoundException`), developers **must verify** that a matching activity exists on the device before executing `startActivity()`.

### Crucial Safety Check Pattern: `resolveActivity()`
Use `resolveActivity(getPackageManager())` to query the system Package Manager.

```java
// 1. Construct implicit intent
Intent mapIntent = new Intent(Intent.ACTION_VIEW);
mapIntent.setData(Uri.parse("geo:37.7749,-122.4194?q=restaurants"));

// 2. Perform defensive resolution check before launching
if (mapIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(mapIntent);
} else {
    Log.w("ImplicitIntent", "No map application installed on device.");
}
```

---

## 4. Forcing the App Chooser (`Intent.createChooser`)

When users perform actions like **Sharing**, they may want to use a different app each time rather than setting a permanent default. `Intent.createChooser()` forces the Android App Chooser dialog to display every time.

```java
Intent sendIntent = new Intent(Intent.ACTION_SEND);
sendIntent.putExtra(Intent.EXTRA_TEXT, "Check out this Android study guide!");
sendIntent.setType("text/plain");

// Create chooser wrapper intent with a custom dialog title
String chooserTitle = getResources().getString(R.string.chooser_title);
Intent chooserIntent = Intent.createChooser(sendIntent, chooserTitle);

// Verify and launch chooser
if (sendIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(chooserIntent);
}
```

---

## 5. Receiving Implicit Intents: Intent Filters (`<intent-filter>`)

To allow external apps to launch an activity in your application, you must declare one or more `<intent-filter>` elements inside your `AndroidManifest.xml`.

### Three Intent Filter Matching Rules (Exam 6-Mark Question):
An incoming implicit intent must pass **all three tests** to be delivered to the target activity:
1. **Action Test**: The intent action must match at least one `<action>` defined in the filter.
2. **Category Test**: Every category declared in the incoming `Intent` object must match a `<category>` tag in the filter. *(Note: All activities handling implicit intents MUST declare `android.intent.category.DEFAULT`)*.
3. **Data Test**: The URI scheme and MIME type must match the filter's `<data>` tag.

### Sample Manifest Declaration (`AndroidManifest.xml`)

```xml
<activity android:name=".ShareActivity">
    <intent-filter>
        <!-- Accepts ACTION_SEND requests -->
        <action android:name="android.intent.action.SEND" />
        <!-- Mandatory category for implicit intents -->
        <category android:name="android.intent.category.DEFAULT" />
        <!-- Specifies accepted MIME data type -->
        <data android:mimeType="text/plain" />
    </intent-filter>
</activity>
```

---

## 6. Practical Sharing with `ShareCompat.IntentBuilder`

Android Support Library (V4 / AndroidX) provides `ShareCompat.IntentBuilder` to simplify building cross-app sharing intents with backward compatibility across older Android versions.

```java
ShareCompat.IntentBuilder
    .from(this)                               // Calling activity context
    .setType("text/plain")                    // MIME type
    .setChooserTitle("Share text via:")       // Chooser title
    .setText("Hello from ShareCompat!")       // Text payload
    .startChooser();                          // Send intent
```

---

## 7. Activity Launch Modes (Exam 8-Mark Core Question)

Launch modes dictate how a new Activity instance is created and placed inside the current or new **Task Back Stack**.

Specified via `android:launchMode` attribute in `AndroidManifest.xml` or via **Intent Flags**.

### The 4 Activity Launch Modes:

| Launch Mode | Back Stack & Instantiation Behavior | Use Case |
| :--- | :--- | :--- |
| **`standard`** *(Default)* | Always creates a new instance of the Activity on top of the current task's back stack. Multiple instances can exist in the same task. | Standard screens (e.g., news article detail view). |
| **`singleTop`** | If an instance of the activity **already exists at the top of the current back stack**, the intent is routed to it via `onNewIntent()` instead of creating a new instance. | Search results screen, notification targets. |
| **`singleTask`** | System creates a new task root for the activity. If an instance already exists in another task, the system brings that task to the foreground and routes the intent via `onNewIntent()`. | Main Home/Dashboard Activity. |
| **`singleInstance`** | Same as `singleTask`, but the system permits **NO OTHER activities** inside the task. The activity is the sole, isolated member of its task. | Device Dialer, Alarm Clock app. |

```xml
<activity
    android:name=".SecondActivity"
    android:launchMode="singleTop" />
```

---

## 8. Intent Flags for Task Management

Intent flags passed to `intent.addFlags()` override manifest `launchMode` settings:

- **`FLAG_ACTIVITY_NEW_TASK`**: Launches the activity in a new task (equivalent to `singleTask`).
- **`FLAG_ACTIVITY_SINGLE_TOP`**: Routes intent to existing top instance (equivalent to `singleTop`).
- **`FLAG_ACTIVITY_CLEAR_TOP`**: If an instance of the target activity exists in the back stack, destroys all activities on top of it and routes the intent to the existing instance.

---

## 9. Handling Re-routed Intents: `onNewIntent()`

When `singleTop` or `singleTask` routes a new intent to an existing activity instance, the system calls `onNewIntent(Intent intent)` instead of recreating the activity.

```java
@Override
protected void onNewIntent(Intent intent) {
    super.onNewIntent(intent);
    
    // Replace original intent returned by getIntent() with the new intent
    setIntent(intent);
    
    // Process new intent data
    handleIncomingData(intent);
}
```

---

## 10. Task Affinities & Reparenting

- **`android:taskAffinity`**: Specifies which task an activity prefers to belong to (defaults to the app's package name). Used with `singleTask` or `FLAG_ACTIVITY_NEW_TASK` to group activities into custom task stacks.
- **`android:allowTaskReparenting="true"`**: Allows an activity to move from the task where it was launched to the task it has an affinity for when that task comes to the foreground.

---

## 11. Complete Executable Java Example: Sending Common Implicit Intents

```java
package com.example.android.implicitintents;

import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.support.v4.app.ShareCompat;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.view.View;
import android.widget.EditText;

public class MainActivity extends AppCompatActivity {

    private EditText mWebsiteEditText;
    private EditText mLocationEditText;
    private EditText mShareTextEditText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mWebsiteEditText = (EditText) findViewById(R.id.website_edittext);
        mLocationEditText = (EditText) findViewById(R.id.location_edittext);
        mShareTextEditText = (EditText) findViewById(R.id.share_edittext);
    }

    // 1. Launch Web Browser via Implicit Intent
    public void openWebsite(View view) {
        String url = mWebsiteEditText.getText().toString();
        Uri webpage = Uri.parse(url);
        Intent intent = new Intent(Intent.ACTION_VIEW, webpage);

        if (intent.resolveActivity(getPackageManager()) != null) {
            startActivity(intent);
        } else {
            Log.d("ImplicitIntents", "Can't handle web URL request!");
        }
    }

    // 2. Launch Map App via Implicit Intent
    public void openLocation(View view) {
        String loc = mLocationEditText.getText().toString();
        Uri addressUri = Uri.parse("geo:0,0?q=" + loc);
        Intent intent = new Intent(Intent.ACTION_VIEW, addressUri);

        if (intent.resolveActivity(getPackageManager()) != null) {
            startActivity(intent);
        } else {
            Log.d("ImplicitIntents", "Can't handle location request!");
        }
    }

    // 3. Share Text via ShareCompat.IntentBuilder
    public void shareText(View view) {
        String txt = mShareTextEditText.getText().toString();
        String mimeType = "text/plain";

        ShareCompat.IntentBuilder
            .from(this)
            .setType(mimeType)
            .setChooserTitle("Share this text with: ")
            .setText(txt)
            .startChooser();
    }
}
```
