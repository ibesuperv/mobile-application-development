# User Interaction & User Input Controls

---

## 1. Introduction to User Interaction & Input Controls

**User Interaction** in Android encompasses all the mechanisms—touch gestures, key presses, text input, and voice commands—by which users communicate with an application.

**Input Controls** are the interactive visual UI components (built on `android.view.View`) that capture user input and trigger application responses.

### Material Design Guidance:
Android UI components conform to Google's **Material Design Specification**. Controls should be intuitive, consistent across apps, and minimize user effort (pre-filling data, minimizing touch gestures, providing clear visual state feedback).

---

## 2. View Focus & Touch Mode (Exam Core Concept)

Android uses **Focus** to determine which specific `View` in the window hierarchy receives key presses or software keyboard input.

### Focus vs. Clickable (Exam 4-Mark Distinction):

| Property | Definition | Key Difference |
| :--- | :--- | :--- |
| **`clickable`** (`android:clickable`) | Indicates whether a View can respond to click or tap events (`OnClickListener`). | Touch targets like `Button` are clickable by default. |
| **`focusable`** (`android:focusable`) | Indicates whether a View is allowed to gain input focus from input devices (keyboard, trackball, D-pad). | Keyboards send key events **only** to the View that currently has focus. |

---

### Touch Mode Behavior:
- **Smartphones / Tablets (Touch Devices)**: Enter "Touch Mode" when touched. In Touch Mode, only Views with `isFocusableInTouchMode="true"` (like `EditText`) take focus when touched. Buttons or Checkboxes react to clicks immediately without taking input focus.
- **TV / Non-Touch Devices (D-Pad Navigation)**: Navigated via trackball or D-pad direction keys. Every focusable element gains a visible focus ring as navigation moves from element to element.

---

### Customizing Focus Order in XML Layouts:
By default, focus moves left-to-right, top-to-bottom. You can explicitly override focus navigation using directional focus attributes:

```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <Button
        android:id="@+id/button_top"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:nextFocusDown="@+id/button_bottom"
        android:nextFocusUp="@+id/button_bottom" />

    <Button
        android:id="@+id/button_bottom"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:nextFocusDown="@+id/button_top"
        android:nextFocusUp="@+id/button_top" />
</LinearLayout>
```

---

## 3. Comprehensive Input Controls Breakdown (10-Mark Core Question)

---

### A. Buttons (`Button` & `ImageButton`)

Buttons perform an action when clicked. They have 3 visual states: **Normal**, **Disabled** (grayed out), and **Pressed** (shadow / ripple effect).

```xml
<!-- 1. Text Button -->
<Button
    android:id="@+id/button_submit"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/submit_text" />

<!-- 2. Text Button with Icon (drawableLeft / Start) -->
<Button
    android:id="@+id/button_icon_text"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:drawableLeft="@drawable/ic_send"
    android:text="@string/send_text" />

<!-- 3. Image-Only Button (ImageButton) -->
<ImageButton
    android:id="@+id/button_image"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:contentDescription="@string/play_hint"
    android:src="@drawable/ic_play" />
```

---

### B. Text Input (`EditText`) & On-Screen Soft Keyboard Tuning

`EditText` extends `TextView`, enabling user text editing.

#### Key XML Attributes for Keyboard & Focus Tuning:
- **`android:inputType`**: Dictates soft keyboard layout (`textPersonName`, `textPassword`, `number`, `phone`, `textEmailAddress`, `textCapSentences`).
- **`android:imeOptions`**: Customizes the bottom-right Action Key on the soft keyboard (`actionNext`, `actionDone`, `actionSearch`, `actionSend`).
- **`android:hint`**: Displays placeholder text when the field is empty.

```xml
<EditText
    android:id="@+id/edit_email"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="@string/enter_email_hint"
    android:imeOptions="actionNext"
    android:inputType="textEmailAddress" />
```

---

### C. Checkboxes (`CheckBox`)

Allows users to select one or more options from a set. Can be toggled on (checked) or off (unchecked).

#### XML Layout:
```xml
<CheckBox
    android:id="@+id/checkbox_chocolate"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/chocolate_topping" />
```

#### Java Event Handler:
```xml
<CheckBox
    android:id="@+id/checkbox_sprinkles"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:onClick="onCheckboxClicked"
    android:text="@string/sprinkles_topping" />
```

```java
public void onCheckboxClicked(View view) {
    // Check if the checkbox is checked
    boolean checked = ((CheckBox) view).isChecked();

    switch (view.getId()) {
        case R.id.checkbox_sprinkles:
            if (checked) {
                // Add sprinkles
            } else {
                // Remove sprinkles
            }
            break;
    }
}
```

---

### D. Radio Buttons & RadioGroup (`RadioButton`, `RadioGroup`)

Radio buttons allow selecting **EXACTLY ONE** option from a mutually exclusive set. They **must be grouped** inside a `<RadioGroup>`.

#### XML Layout:
```xml
<RadioGroup
    android:id="@+id/radio_group_delivery"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <RadioButton
        android:id="@+id/radio_sameday"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="onRadioButtonClicked"
        android:text="@string/same_day_delivery" />

    <RadioButton
        android:id="@+id/radio_nextday"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="onRadioButtonClicked"
        android:text="@string/next_day_delivery" />
</RadioGroup>
```

#### Java Event Handler:
```java
public void onRadioButtonClicked(View view) {
    boolean checked = ((RadioButton) view).isChecked();

    switch (view.getId()) {
        case R.id.radio_sameday:
            if (checked) {
                // Same day delivery selected
            }
            break;
        case R.id.radio_nextday:
            if (checked) {
                // Next day delivery selected
            }
            break;
    }
}
```

---

### E. Toggle Buttons (`ToggleButton` & `Switch`)

Displays an on/off state switch.
- `ToggleButton`: Traditional push button showing text ("ON"/"OFF").
- `Switch`: Modern slider widget representing ON/OFF state (commonly used in settings screens).

```xml
<Switch
    android:id="@+id/switch_wifi"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/wifi_setting" />
```

---

### F. Spinners / Dropdown Menus (`Spinner`)

A `Spinner` provides a drop-down menu for selecting one value from a list of options.

#### 1. Define Options Array (`res/values/strings.xml`):
```xml
<string-array name="labels_array">
    <item>Home</item>
    <item>Work</item>
    <item>Mobile</item>
    <item>Other</item>
</string-array>
```

#### 2. Add Spinner to XML Layout:
```xml
<Spinner
    android:id="@+id/label_spinner"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
```

#### 3. Bind Data via `ArrayAdapter` & Handle Selection in Java:
```java
Spinner spinner = (Spinner) findViewById(R.id.label_spinner);

// Create an ArrayAdapter using the string array and default spinner layout
ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(
    this,
    R.array.labels_array,
    android.R.layout.simple_spinner_item
);

// Specify the layout to use when the list of choices appears
adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);

// Apply the adapter to the spinner
spinner.setAdapter(adapter);

// Set Item Selection Listener
spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
        String selectedItem = parent.getItemAtPosition(position).toString();
        Toast.makeText(getApplicationContext(), "Selected: " + selectedItem, Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onNothingSelected(AdapterView<?> parent) {
        // Callback when selection vanishes
    }
});
```

---

## 4. Complete Executable Java Example: Comprehensive Form with Controls

```java
package com.example.android.inputcontroldemo;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.CheckBox;
import android.widget.EditText;
import android.widget.RadioButton;
import android.widget.Spinner;
import android.widget.Toast;

public class OrderActivity extends AppCompatActivity implements AdapterView.OnItemSelectedListener {

    private EditText mNameEditText;
    private String mSpinnerLabel = "";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_order);

        mNameEditText = (EditText) findViewById(R.id.name_edittext);

        // Setup Spinner
        Spinner spinner = (Spinner) findViewById(R.id.label_spinner);
        if (spinner != null) {
            spinner.setOnItemSelectedListener(this);
            ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(
                this, R.array.labels_array, android.R.layout.simple_spinner_item);
            adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
            spinner.setAdapter(adapter);
        }
    }

    public void onRadioButtonClicked(View view) {
        boolean checked = ((RadioButton) view).isChecked();
        switch (view.getId()) {
            case R.id.sameday:
                if (checked) displayToast("Same day ground delivery");
                break;
            case R.id.nextday:
                if (checked) displayToast("Next day delivery");
                break;
        }
    }

    public void displayToast(String message) {
        Toast.makeText(getApplicationContext(), message, Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
        mSpinnerLabel = adapterView.getItemAtPosition(i).toString();
        displayToast(mSpinnerLabel);
    }

    @Override
    public void onNothingSelected(AdapterView<?> adapterView) {
    }
}
```
