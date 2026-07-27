# Question Paper Solutions (Part 2): Layouts & UI Components

---

## Question 9: List and Explain the Following with Functions & Attributes (10 Marks)

### A. LinearLayout (5 Marks)
A **`LinearLayout`** is a view group that aligns all child views in a single direction, either **vertically** or **horizontally**.

```
       VERTICAL LINEARLAYOUT                    HORIZONTAL LINEARLAYOUT
     +-----------------------+                +----------+----------+----------+
     |     Child View 1      |                |  Child   |  Child   |  Child   |
     +-----------------------+                |  View 1  |  View 2  |  View 3  |
     |     Child View 2      |                +----------+----------+----------+
     +-----------------------+
```

#### Functions & Features:
- Arranges views sequentially in a single line.
- Supports weighted distribution of remaining screen space among children using `layout_weight`.

#### Key Attributes:
- **`android:orientation`**: Specifies direction (`vertical` or `horizontal`).
- **`android:layout_weight`**: Assigns an importance/weight ratio (float) to specify how much extra screen space a view should occupy.
- **`android:gravity`**: Controls alignment of child views inside the layout (e.g. `center`, `right`).

---

### B. TableLayout (5 Marks)
A **`TableLayout`** is a view group that arranges child views into **rows and columns** (similar to an HTML `<table>`).

```
                          TABLELAYOUT STRUCTURE
     +-------------------------------------------------------------+
     | TableRow 1: [ TextView Label ]  [ EditText Input Field    ] |
     +-------------------------------------------------------------+
     | TableRow 2: [ Button Submit   ]  [ Button Cancel          ] |
     +-------------------------------------------------------------+
```

#### Functions & Features:
- Each row is defined using a **`<TableRow>`** view group container.
- Columns expand automatically to fit the widest cell in that column.

#### Key Attributes:
- **`android:stretchColumns`**: Specifies zero-based column indices that can stretch to fill remaining screen space (e.g. `android:stretchColumns="1"` or `"*"` for all).
- **`android:shrinkColumns`**: Specifies column indices that can shrink if text overflows.
- **`android:collapseColumns`**: Hides specific columns from display.
- **`android:layout_column`**: Assigns a view to a specific column index inside a `TableRow`.

---

## Question 10: List and Explain the Following with Functions & Attributes (10 Marks)

### A. GridLayout (5 Marks)
A **`GridLayout`** (API 14+) places child views in a **2D grid of cell coordinates**, supporting cell spanning across multiple rows or columns without requiring nested layout structures.

```
                           GRIDLAYOUT STRUCTURE
     +------------------------------+------------------------------+
     | Row 0, Col 0 (Cell 1)        | Row 0, Col 1 (Cell 2)        |
     +------------------------------+------------------------------+
     | Row 1, Col 0 - Col 1 (Spanned Across 2 Columns)             |
     +-------------------------------------------------------------+
```

#### Functions & Features:
- Flattens complex UI designs by eliminating nested `LinearLayouts`.
- Allows views to span across multiple adjacent rows and columns.

#### Key Attributes:
- **`android:rowCount`**: Total number of rows in the grid.
- **`android:columnCount`**: Total number of columns in the grid.
- **`android:layout_rowSpec` & `android:layout_columnSpec`**: Specifies row/column position and span count (e.g. `android:layout_columnSpan="2"`).

---

### B. ListLayout (`ListView`) (5 Marks)
A **`ListView`** (List Layout) is a view group that displays a scrollable, single-column vertical list of items backed by an **`Adapter`**.

```
                        LISTVIEW / LISTLAYOUT
     +-------------------------------------------------------------+
     | Item Row 0 (Title / Subtitle)                               |
     +-------------------------------------------------------------+
     | Item Row 1 (Title / Subtitle)                               |
     +-------------------------------------------------------------+
     | Item Row 2 (Title / Subtitle)                               |
     +-------------------------------------------------------------+
```

#### Functions & Features:
- Recycles list row views to conserve memory when scrolling through large datasets.
- Connects to dynamic data sources (Arrays, Database Cursors) using an `ArrayAdapter` or `SimpleCursorAdapter`.

#### Key Attributes:
- **`android:divider`**: Specifies a drawable/color to draw between list items (e.g. `@color/gray`).
- **`android:dividerHeight`**: Sets the height/thickness of item divider lines (e.g. `1dp`).
- **`android:entries`**: Points to a static string-array resource (`@array/items`).

---

## Question 11: List and Explain the Following with Functions & Attributes (10 Marks)

### A. AbsoluteLayout (5 Marks)
An **`AbsoluteLayout`** enables positioning of child views at **exact $x$ and $y$ coordinate pixel locations** on the screen.

> [!CAUTION]
> **Deprecated**: `AbsoluteLayout` is deprecated because hardcoded pixel coordinates fail to adapt across varying screen sizes, densities, and aspect ratios.

#### Key Attributes:
- **`android:layout_x`**: Specifies the exact horizontal X-coordinate in pixels from the left edge.
- **`android:layout_y`**: Specifies the exact vertical Y-coordinate in pixels from the top edge.

---

### B. RelativeLayout (5 Marks)
A **`RelativeLayout`** positions child views in **relation to sibling views** or in **relation to the parent boundary**.

```
                         RELATIVELAYOUT POSITIONS
     +-------------------------------------------------------------+
     | [ View A (Top Left) ]                                       |
     |                       [ View B (layout_toRightOf="View A") ]|
     |                                                             |
     | [ View C (layout_below="View A") ]                          |
     +-------------------------------------------------------------+
```

#### Functions & Features:
- Eliminates deeply nested layouts by specifying positional relationships between views.

#### Key Attributes:
- **Sibling Positional Attributes**: `android:layout_toRightOf`, `android:layout_toLeftOf`, `android:layout_below`, `android:layout_above`.
- **Parent Positional Attributes**: `android:layout_alignParentTop`, `android:layout_alignParentBottom`, `android:layout_centerInParent`.

---

## Question 12: List and Explain the Following with Functions & Attributes (10 Marks)

### A. ConstraintLayout (5 Marks)
**`ConstraintLayout`** (AndroidX Standard) is a flexible layout manager that allows complex, flat view hierarchies to be positioned using **visual constraints** connected to other views or guidelines.

```
                        CONSTRAINTLAYOUT CONSTRAINTS
     +-------------------------------------------------------------+
     |  (Top Constraint)                                           |
     |        ↓                                                    |
     |  [ Button View ] ←(Left)─────(Right)→ [ Parent Edge ]      |
     |        ↓                                                    |
     |  (Bottom Constraint to Sibling)                             |
     +-------------------------------------------------------------+
```

#### Functions & Features:
- Completely flattens view hierarchies, maximizing screen rendering performance (solving the 16ms frame drop issue).
- Fully supported by Android Studio visual drag-and-drop Layout Editor.

#### Key Attributes:
- **`app:layout_constraintTop_toTopOf`**, **`app:layout_constraintBottom_toBottomOf`**
- **`app:layout_constraintStart_toStartOf`**, **`app:layout_constraintEnd_toEndOf`**
- **`app:layout_constraintHorizontal_bias`**: Float value (`0.0` to `1.0`) controlling horizontal positioning bias.

---

### B. TabLayout (5 Marks)
A **`TabLayout`** (Material Component) provides a horizontal bar of clickable tabs used to switch between different views or fragments, usually paired with a **`ViewPager`**.

```
                            TABLAYOUT STRUCTURE
     +-----------------------+-----------------------+-----------------------+
     |     TAB 1 (Home)      |     TAB 2 (Chats)     |    TAB 3 (Profile)    |
     +-----------------------+-----------------------+-----------------------+
     |                                                                       |
     |                ViewPager Content Area (Fragment Page)                 |
     +-----------------------------------------------------------------------+
```

#### Functions & Features:
- Provides swipeable tabbed navigation adhering to Material Design standards.

#### Key Attributes:
- **`app:tabMode`**: `fixed` (all tabs visible on screen) or `scrollable` (horizontally scrollable tabs).
- **`app:tabGravity`**: `fill` or `center`.
- **`app:tabIndicatorColor`**: Color of the active tab underline indicator.
