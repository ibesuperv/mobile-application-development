# Question Paper Solutions (Part 5): Broadcasts, Email, Timers & Parameter Passing

---

## Question 3 (2020-2021): Broadcast Receivers & Email Sending Code (10 Marks)

### A. Implicit vs Explicit Broadcast Receivers & System/App Events (6 Marks)

#### 1. Implicit vs. Explicit Broadcast Receivers:
- **Implicit Broadcast Receiver**: Listens for general system-wide broadcasts that do NOT specify a specific target package (e.g. `ACTION_BOOT_COMPLETED`, `ACTION_BATTERY_LOW`). Delivered to any registered receiver.
- **Explicit Broadcast Receiver**: Listens for targeted broadcasts that explicitly define the receiver's component class name (e.g. `intent.setComponent(new ComponentName(pkg, cls))`). Delivered ONLY to that specific app.

#### 2. System Events vs. Application Events:
- **System Events**: Broadcasts generated automatically by the Android OS when device state changes occur (e.g., `ACTION_POWER_CONNECTED`, `ACTION_AIRPLANE_MODE_CHANGED`).
- **Application Events**: Custom broadcasts dispatched by an application using `sendBroadcast(intent)` to communicate internally across app components or to companion apps.

---

### B. `MainActivity.java` Code for Email Sending (4 Marks)

```java
package com.example.emailapp;

import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    private EditText mEtTo, mEtSubject, mEtMessage;
    private Button mBtnSend;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mEtTo = findViewById(R.id.et_to);
        mEtSubject = findViewById(R.id.et_subject);
        mEtMessage = findViewById(R.id.et_message);
        mBtnSend = findViewById(R.id.btn_send);

        mBtnSend.setOnClickListener(v -> sendEmail());
    }

    private void sendEmail() {
        String recipient = mEtTo.getText().toString().trim();
        String subject = mEtSubject.getText().toString().trim();
        String message = mEtMessage.getText().toString().trim();

        // Create Implicit Email Intent using mailto: URI scheme
        Intent emailIntent = new Intent(Intent.ACTION_SENDTO);
        emailIntent.setData(Uri.parse("mailto:")); // Ensures only email client apps respond
        emailIntent.putExtra(Intent.EXTRA_EMAIL, new String[]{ recipient });
        emailIntent.putExtra(Intent.EXTRA_SUBJECT, subject);
        emailIntent.putExtra(Intent.EXTRA_TEXT, message);

        if (emailIntent.resolveActivity(getPackageManager()) != null) {
            startActivity(emailIntent);
        } else {
            Toast.makeText(this, "No Email app found!", Toast.LENGTH_SHORT).show();
        }
    }
}
```

---

## Question 4 (2020-2021): CountDownTimer Android App Code (10 Marks)

App displaying a countdown timer ticking down from 30 seconds to 0.

### A. Layout File (`activity_main.xml`)
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="16dp">

    <TextView
        android:id="@+id/tv_timer"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="00:30"
        android:textSize="48sp"
        android:textStyle="bold"
        android:textColor="#000000" />

    <Button
        android:id="@+id/btn_start"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="START COUNTDOWN"
        android:layout_marginTop="24dp" />

</LinearLayout>
```

---

### B. Java File (`MainActivity.java`)
```java
package com.example.timerapp;

import android.os.Bundle;
import android.os.CountDownTimer;
import android.widget.Button;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    private TextView mTvTimer;
    private Button mBtnStart;
    private CountDownTimer mCountDownTimer;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mTvTimer = findViewById(R.id.tv_timer);
        mBtnStart = findViewById(R.id.btn_start);

        mBtnStart.setOnClickListener(v -> startTimer());
    }

    private void startTimer() {
        // CountDownTimer(total_duration_ms, interval_ms) -> 30 seconds total, tick every 1 second
        mCountDownTimer = new CountDownTimer(30000, 1000) {
            @Override
            public void onTick(long millisUntilFinished) {
                int secondsRemaining = (int) (millisUntilFinished / 1000);
                mTvTimer.setText(String.format("00:%02d", secondsRemaining));
            }

            @Override
            public void onFinish() {
                mTvTimer.setText("TIME UP!");
            }
        }.start();
    }
}
```

---

## Question 5 (2020-2021): Activities, Intents & Parameter Passing Code (10 Marks)

### A. Concept Explanation (3 Marks)
- **Activity**: Represents a single interactive UI screen.
- **Intent**: A messaging object used to navigate between activities and transfer data parameters using **Extras (`putExtra()`)**.

---

### B. Activity 1: `activity1_main.xml` & `MainActivity.java` (3.5 Marks)

#### `activity1_main.xml`:
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <Button
        android:id="@+id/btn_open_activity2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="OPEN ACTIVITY 2" />

</LinearLayout>
```

#### `MainActivity.java`:
```java
package com.example.passparamapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity1_main);

        Button btnOpen = findViewById(R.id.btn_open_activity2);
        btnOpen.setOnClickListener(v -> {
            // Explicit Intent to open Activity2 with String extra parameter
            Intent intent = new Intent(MainActivity.this, Activity2.class);
            intent.putExtra("INIT_MSG", "ACTIVITY2 INITIATED");
            startActivity(intent);
        });
    }
}
```

---

### C. Activity 2: `activity2_main.xml` & `Activity2.java` (3.5 Marks)

#### `activity2_main.xml`:
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tv_activity2_msg"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="20sp"
        android:textColor="#000000" />

</LinearLayout>
```

#### `Activity2.java`:
```java
package com.example.passparamapp;

import android.os.Bundle;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class Activity2 extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity2_main);

        TextView tvMsg = findViewById(R.id.tv_activity2_msg);

        // Extract passed Intent parameter
        if (getIntent() != null && getIntent().hasExtra("INIT_MSG")) {
            String message = getIntent().getStringExtra("INIT_MSG");
            tvMsg.setText(message);
        }
    }
}
```
