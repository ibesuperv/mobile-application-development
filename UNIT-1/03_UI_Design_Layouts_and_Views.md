# UI Design: Building Layouts with UI Elements, Layouts & Views

---

## 1. Model-View-Presenter (MVP) Architecture Pattern (6-Mark Question)

Linking an Activity component to an XML layout resource demonstrates the **Model-View-Presenter (MVP)** architectural pattern. It separates concerns into three decoupled layers:

```
+-------------------------------------------------------------------+
|                            VIEWS (UI)                             |
|  (TextView, Button, EditText, Layout XMLs - Displays Data & Inputs)|
+-------------------------------------------------------------------+
                                ^   |
                  Data Updates  |   | User Interaction Events
                                |   v
+-------------------------------------------------------------------+
|                        PRESENTERS (LOGIC)                         |
|  (Activity / Controller Class - Handles Logic & State Transitions)|
+-------------------------------------------------------------------+
                                ^   |
                 Data Fetching  |   | Data Updates / Storage
                                |   v
+-------------------------------------------------------------------+
|                           MODEL (DATA)                            |
|  (Data Structure, Database, Preferences, Business Domain Logic)   |
+-------------------------------------------------------------------+
```

### Detailed Component Roles:

1. **Views**: User interface elements that display data to the user and listen for user interactions (taps, swipes, text entry). Every screen element is a `View`.
2. **Presenters**: Acts as the intermediary linking Views to the Model. Receives user actions from the View, processes application logic, requests data from the Model, and updates the View UI state accordingly.
3. **Model**: Encapsulates data persistence, database APIs, network requests, and domain business rules.

---

## 2. Hierarchy of Views and ViewGroups

In Android, the user interface is constructed as a **Tree Hierarchy of View objects**.

```
                           ViewGroup (Root Layout)
                                /          \
                               /            \
                     View (e.g. TextView)   ViewGroup (Child Layout)
                                                 /             \
                                                /               \
                                     View (Button)       View (EditText)
```

### A. View (`android.view.View`)

- The fundamental base building block for all user interface components.
- Occupies a rectangular bounding region on screen and handles drawing graphics and capturing touch/click events.
- **Coordinates & Dimensions**: Expressed using $(x, y)$ coordinates (left and top edges) and dimensions (width and height) in **dp (device-independent pixels)**.
- **Core View Types**:
  - `TextView`: Displays static or dynamic formatted text strings.
  - `EditText`: Interactive text field allowing user text entry and editing.
  - `Button`: Clickable push button triggering action events.
  - `ImageView`: Displays bitmap images (`PNG`, `JPG`) or vector drawables.
  - `ScrollView`: Special container accommodating one single scrollable child view hierarchy.
  - `RecyclerView`: High-performance scrollable view container recycling views dynamically.

### B. ViewGroup (`android.view.ViewGroup`)

- An abstract subclass of `View` that functions as a invisible container holding child views and nested view groups.
- Establishes a strict **Parent-Child relationship** governing layout dimensions and positioning.

---

## 3. Layout ViewGroups (Visual Structural Diagrams - 8/10-Mark Exam Core Question)

Layouts are specialized `ViewGroup` containers that organize child views according to explicit positional algorithms.

### 1. `LinearLayout`

Arranges child views sequentially in a single line, either vertically or horizontally.

```
Vertical Orientation                        Horizontal Orientation
+-----------------------+                   +-----------------------+
|  +-----------------+  |                   | +-------+  +-------+  |
|  |     View 1      |  |                   | | View 1|  | View 2|  |
|  +-----------------+  |                   | |       |  |       |  |
|  |     View 2      |  |                   | +-------+  +-------+  |
|  +-----------------+  |                   +-----------------------+
+-----------------------+
```

- **Key Attributes**:
  - `android:orientation`: `vertical` or `horizontal`.
  - `android:layout_weight`: Specifies dynamic space distribution proportions among child views.

---

### 2. `RelativeLayout`

Positions child views relative to sibling views (by ID) or relative to parent layout boundaries.

```
+---------------------------------------------------+
| View 1 (alignParentTop="true")                    |
| +-----------------------------------------------+ |
| |                    VIEW 1                     | |
| +-----------------------------------------------+ |
|                                                   |
| View 2 (below="@id/view1", alignParentLeft)       |
| +-----------------+                               |
| |     VIEW 2      |  View 3 (toRightOf="@id/view2")|
| +-----------------+  +--------------------------+ |
|                      |          VIEW 3          | |
|                      +--------------------------+ |
+---------------------------------------------------+
```

- **Key Attributes**:
  - Relative to siblings: `android:layout_toRightOf="@id/target"`, `android:layout_below="@id/target"`.
  - Relative to parent: `android:layout_alignParentTop="true"`, `android:layout_centerInParent="true"`.

---

### 3. `ConstraintLayout`

High-performance layout manager utilizing anchor handles and guidelines to position views without nesting.

```
+---------------------------------------------------+
|           [Top Parent Constraint]                 |
|                      |                            |
| [Left] ----(o) View A (o)---- [Right Parent]      |
|                      |                            |
|             (Baseline Alignment)                  |
|                      |                            |
|             (o) View B (o)                        |
+---------------------------------------------------+
```

- **Handles**:
  - **Resize Handles**: Corner squares to adjust width/height.
  - **Constraint Handles**: Side circles connecting to other views or parent edges.
  - **Baseline Handles**: Align text baselines across different views.

---

### 4. `FrameLayout`

Stacks child views on top of each other along the Z-axis (overlapping stack), with the newest child drawn on top.

```
+---------------------------------------+
| FrameLayout Boundary                  |
|  +---------------------------------+  |  (View 1: Background Image)
|  |            VIEW 1               |  |
|  |   +--------------------------+  |  |  (View 2: Text Overlay drawn ON TOP)
|  |   |    VIEW 2 (Overlaid)     |  |  |
|  |   +--------------------------+  |  |
|  +---------------------------------+  |
+---------------------------------------+
```

---

### 5. `TableLayout`

Arranges views into tabular rows and columns using `<TableRow>` containers.

```
+---------------------------------------+
| TableLayout                           |
| Row 1:  [ Col 1 Text ] [ Col 2 Btn ]  |
| Row 2:  [ Col 1 Edit ] [ Col 2 Text]  |
+---------------------------------------+
```

---

### 6. `AbsoluteLayout` _(Deprecated)_

Positions child views using explicit $(X, Y)$ coordinate pixel offsets (`android:layout_x`, `android:layout_y`). Highly discouraged due to incompatibility across variable device screen sizes.

---

## 4. Layout XML Attributes & View Properties (Exam Technical Definitions)

### A. Mandatory Dimensions: `layout_width` and `layout_height`

- `match_parent`: View expands to occupy the exact dimensions of its parent container.
- `wrap_content`: View resizes tightly to enclose its internal content (text or image).
- `Fixed dp Value` (e.g., `48dp`): Assigns an absolute size in device-independent pixels.

### B. Resource Identifiers (`@+id/` vs `@id/`)

- `@+id/view_name`: The `+` symbol instructs AAPT compiler to **create a new resource ID** inside `R.java`.
- `@id/view_name`: Omits `+` to **reference an existing resource ID** previously declared elsewhere.

### C. Gravity vs. Layout Gravity (Core Distinction)

- `android:gravity`: Controls alignment of **internal content inside the view bounding box**.
- `android:layout_gravity`: Controls alignment of **the view itself inside its parent ViewGroup container**.

### D. Padding vs. Margin (Exam Visual Diagram & Comparison)

| Dimension   | Technical Definition                                                                                                                               | XML Attributes                                                                                                                               |
| :---------- | :------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------- |
| **Margin**  | Empty space **outside** the view's border, separating the view from its parent container edges or sibling views.                                   | `android:layout_margin`, `android:layout_marginTop`, `android:layout_marginBottom`, `android:layout_marginStart`, `android:layout_marginEnd` |
| **Padding** | Empty space **inside** the view's border, separating the view's outer boundary from its inner content (text/graphic). Included in total view size. | `android:padding`, `android:paddingTop`, `android:paddingBottom`, `android:paddingStart`, `android:paddingEnd`                               |

---

## 5. Complete Exam-Ready XML Layout & Java Code (`HelloToast`)

### `activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    android:background="#F5F5F5"
    tools:context=".MainActivity">

    <!-- Toast Trigger Button -->
    <Button
        android:id="@+id/button_toast"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#3F51B5"
        android:textColor="#FFFFFF"
        android:textSize="16sp"
        android:text="@string/button_label_toast" />

    <!-- Counter Display TextView -->
    <TextView
        android:id="@+id/show_count"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:gravity="center"
        android:background="#FFFF00"
        android:text="0"
        android:textColor="#3F51B5"
        android:textSize="160sp"
        android:textStyle="bold" />

    <!-- Count Increment Button -->
    <Button
        android:id="@+id/button_count"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#3F51B5"
        android:textColor="#FFFFFF"
        android:textSize="16sp"
        android:text="@string/button_label_count" />

</LinearLayout>
```

### `MainActivity.java`

```java
package com.example.android.hellotoast;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

/**
 * Activity implementing the HelloToast UI layout logic.
 * Demonstrates View lookup via findViewById, event listeners, and dynamic UI updates.
 */
public class MainActivity extends AppCompatActivity {

    private int mCount = 0;
    private TextView mShowCount;
    private Button mButtonToast;
    private Button mButtonCount;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // Inflate the XML layout into the Activity view window
        setContentView(R.layout.activity_main);

        // Bind Java object references to XML view elements via resource IDs
        mShowCount = (TextView) findViewById(R.id.show_count);
        mButtonToast = (Button) findViewById(R.id.button_toast);
        mButtonCount = (Button) findViewById(R.id.button_count);

        // Register Click Listener for Toast Button
        mButtonToast.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                showToast();
            }
        });

        // Register Click Listener for Count Increment Button
        mButtonCount.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                countUp();
            }
        });
    }

    /**
     * Displays a transient Toast notification message on screen.
     */
    private void showToast() {
        Toast toast = Toast.makeText(this, R.string.toast_message, Toast.LENGTH_SHORT);
        toast.show();
    }

    /**
     * Increments internal counter state and updates the TextView UI display.
     */
    private void countUp() {
        mCount++;
        if (mShowCount != null) {
            mShowCount.setText(Integer.toString(mCount));
        }
    }
}
```
