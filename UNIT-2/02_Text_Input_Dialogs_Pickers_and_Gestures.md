# Text Input, Dialogs, Pickers & Gesture Recognition

---

## 1. Advanced Text Input Customization (`EditText`)

`EditText` is the primary interactive view for receiving user text entry.

### A. Key Customization XML Attributes (Exam 6-Mark Question)

- **`android:inputType`**: Dictates the soft keyboard layout and input behavior:
  - `textPersonName` / `textCapSentences`: Capitalizes sentences automatically.
  - `textEmailAddress`: Shows an email-optimized soft keyboard with `@` and `.com` keys.
  - `phone`: Opens a numeric phone keypad.
  - `textPassword`: Obscures typed characters into dots for privacy.
  - Combine attributes using Bitwise OR (`|`): `android:inputType="textAutoCorrect|textCapSentences"`
- **`android:imeOptions`**: Changes the bottom-right Soft Keyboard Action Key:
  - `actionNext`: Moves focus to the next `EditText` field.
  - `actionDone`: Dismisses the soft keyboard.
  - `actionSend`: Triggers a submission or send callback.
- **Limiting & Formatting Attributes**:
  - `android:maxLines="1"`: Restricts input display to a single line.
  - `android:maxLength="10"`: Hard limit on character count.
  - `android:digits="01"`: Restricts input strictly to binary digits '0' and '1'.
  - `android:hint="@string/hint_text"`: Displays placeholder text.

### B. Customizing Keyboard Action Key Programmatically (`setOnEditorActionListener`)

```java
EditText editText = (EditText)findViewById(R.id.editText);

editText.setOnEditorActionListener(new TextView.OnEditorActionListener() {

    @Override
    public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {
        if(actionId == EditorInfo.IME_ACTION_DONE){
            Toast.makeText(getApplicationContext(), "Done Pressed", Toast.LENGTH_SHORT).show();
            return true;
        }
        return false;
    }
});
```

---

## 2. Using Dialogs (`AlertDialog`)

A **Dialog** is a small modal window that appears on top of the current Activity, capturing exclusive focus until dismissed or an action is taken.

### A. Anatomical Structure of an Alert Dialog (Exam 4-Mark Diagram/Breakdown)

```
+-------------------------------------------------------+
|  1. Title Area (Optional)                             |
|     "Discard draft?"                                  |
+-------------------------------------------------------+
|  2. Content Area                                      |
|     "There are unsaved changes. Are you sure you      |
|      want to quit without saving?"                    |
+-------------------------------------------------------+
|  3. Action Buttons                                    |
|     [DISCARD (Negative)]   [CANCEL (Neutral)]   [OK (Positive)] |
+-------------------------------------------------------+
```

---

### B. Creating an Alert Dialog using `AlertDialog.Builder`

`AlertDialog.Builder` implements the **Builder Design Pattern** to configure and instantiate complex dialog objects cleanly.

```java
public void showAlert(View view){

    AlertDialog.Builder builder = new AlertDialog.Builder(this);

    builder.setTitle("Alert");

    builder.setMessage("Are you sure?");

    builder.setPositiveButton("OK", new DialogInterface.OnClickListener() {

        @Override
        public void onClick(DialogInterface dialog, int which) {
            Toast.makeText(getApplicationContext(), "OK Pressed", Toast.LENGTH_SHORT).show();
        }
    });

    builder.show();

}
```

---

## 3. Date & Time Pickers (`DialogFragment`)

Pickers provide standardized, locale-sensitive controls for selecting dates and times. They should be wrapped inside a **`DialogFragment`** to properly manage dialog lifecycles and screen rotation.

### A. Date Picker Implementation (`DatePickerFragment.java`)

```java
public class DatePickerFragment extends DialogFragment
        implements DatePickerDialog.OnDateSetListener {

    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {

        Calendar c = Calendar.getInstance();

        int year = c.get(Calendar.YEAR);
        int month = c.get(Calendar.MONTH);
        int day = c.get(Calendar.DAY_OF_MONTH);

        return new DatePickerDialog(getActivity(), this, year, month, day);
    }

    @Override
    public void onDateSet(DatePicker view, int year, int month, int day) {

        MainActivity activity = (MainActivity)getActivity();

        activity.processDatePickerResult(year, month, day);

    }

}
```

#### Launching the DatePicker Fragment from `MainActivity.java`:

```java
public void showDatePicker(View view){

    DialogFragment picker = new DatePickerFragment();

    picker.show(getSupportFragmentManager(), "datePicker");

}

public void processDatePickerResult(int year, int month, int day){
    month = month + 1;
    String date = day + "/" + month + "/" + year;

    Toast.makeText(this, date, Toast.LENGTH_SHORT).show();

}
```

---

## 4. Recognizing Touch Gestures (Exam 8-Mark Core Question)

A **Touch Gesture** occurs when a user places one or more fingers on the touch screen and performs specific movement patterns (e.g., tap, double-tap, long press, fling/swipe, scroll).

### A. Anatomy of a Motion Event (`MotionEvent`)

Touch interactions deliver `MotionEvent` objects to the Activity's `onTouchEvent(MotionEvent event)` callback.

- **Action Codes**: State transitions:
  - `MotionEvent.ACTION_DOWN`: First finger touches the screen (gesture starts).
  - `MotionEvent.ACTION_MOVE`: Finger moves across the screen.
  - `MotionEvent.ACTION_UP`: Last finger leaves the screen (gesture ends).
  - `MotionEvent.ACTION_CANCEL`: Gesture is interrupted or canceled.
  - `MotionEvent.ACTION_POINTER_DOWN` / `ACTION_POINTER_UP`: Secondary fingers for multi-touch gestures.
- **Pointers**: Unique integer IDs assigned to individual active touch contact points. Coordinates are queried using `event.getX(pointerIndex)` and `event.getY(pointerIndex)`.

---

### B. Detecting Low-Level Touch Events (`onTouchEvent`)

```java
@Override
public boolean onTouchEvent(MotionEvent event) {

    switch(event.getAction()) {
        case MotionEvent.ACTION_DOWN:
            Toast.makeText(this, "DOWN", Toast.LENGTH_SHORT).show();
            break;

        case MotionEvent.ACTION_MOVE:
            Toast.makeText(this, "MOVE", Toast.LENGTH_SHORT).show();
            break;

        case MotionEvent.ACTION_UP:
            Toast.makeText(this, "UP", Toast.LENGTH_SHORT).show();
            break;
    }

    return true;
}
```

---

### C. Detecting Common High-Level Gestures via `GestureDetectorCompat`

Rather than manually parsing raw `MotionEvent` vectors, Android provides `GestureDetectorCompat` combined with `GestureDetector.SimpleOnGestureListener`.

#### Executable Gesture Detection Pattern:

```java
package com.example.android.gesturedemo;

import android.os.Bundle;
import android.support.v4.view.GestureDetectorCompat;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.view.GestureDetector;
import android.view.MotionEvent;

public class MainActivity extends AppCompatActivity {

    private GestureDetectorCompat mDetector;
    private static final String DEBUG_TAG = "Gestures";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Instantiate detector with custom gesture listener
        mDetector = new GestureDetectorCompat(this, new MyGestureListener());
    }

    // Intercept all touch events and pass them to GestureDetectorCompat
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        this.mDetector.onTouchEvent(event);
        return super.onTouchEvent(event);
    }

    // Custom Gesture Listener extending SimpleOnGestureListener
    private class MyGestureListener extends GestureDetector.SimpleOnGestureListener {

        @Override
        public boolean onDown(MotionEvent event) {
            Log.d(DEBUG_TAG, "onDown: " + event.toString());
            return true; // Must return true to process subsequent gesture events
        }

        @Override
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
            Log.d(DEBUG_TAG, "onFling: VelocityX=" + velocityX + " VelocityY=" + velocityY);
            return true;
        }

        @Override
        public boolean onSingleTapConfirmed(MotionEvent e) {
            Log.d(DEBUG_TAG, "onSingleTapConfirmed");
            return true;
        }

        @Override
        public void onLongPress(MotionEvent e) {
            Log.d(DEBUG_TAG, "onLongPress");
        }
    }
}
```
