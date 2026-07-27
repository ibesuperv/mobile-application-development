# Resources, Text and Scrolling Views

---

## 1. Android Resource Architecture & Resource Files

In Android application development, **Resources** separate static data and visual assets from Java program logic. Decoupling resources allows developers to modify UI strings, colors, dimensions, and layouts without modifying or recompiling Java code, and enables seamless **Internationalization (Localization)** and multi-screen adaptation.

### Resource Folder Structure (`app/res/`)

```
app/res/
 ├── drawable/     (Bitmap images, XML vector graphics, shape drawables)
 ├── layout/       (XML layout resource files defining View hierarchies)
 ├── menu/         (XML files defining application options/action menus)
 ├── mipmap/       (App launcher icons optimized for screen densities: mdpi -> xxxhdpi)
 └── values/       (XML resource value tables)
      ├── strings.xml  (Text strings for UI localization)
      ├── colors.xml   (Hexadecimal color definitions)
      ├── dimens.xml   (Device-independent dimension measurements)
      └── styles.xml   (Theme attributes and visual styling rules)
```

---

## 2. Resource Reference Syntax (XML vs. Java)

### A. Referencing Resources in XML Layouts
The syntax for referencing a resource inside XML layout files is:
`@package_name:resource_type/resource_name`

1. **Project Resources (Same Package)**: Omits `package_name` because the resource lives inside the project's `res/` folder.
   - String: `android:text="@string/button_label_toast"`
   - Color: `android:background="@color/colorPrimary"`
   - Dimension: `android:paddingLeft="@dimen/activity_horizontal_margin"`

2. **System Resources (Android Platform)**: Requires the `android:` package prefix followed by a colon.
   - White System Color: `android:textColor="@android:color/white"`
   - Predefined Text Appearance: `android:textAppearance="@android:style/TextAppearance"`

### B. Referencing Resources in Java Code
The `aapt` (Android Asset Packaging Tool) automatically compiles resource IDs into static integer constants inside the **`R.java`** class.

- Accessing String in Java: `String appName = getString(R.string.app_name);`
- Accessing Color in Java: `int color = ContextCompat.getColor(context, R.color.colorPrimary);`
- Finding View by Resource ID: `TextView title = (TextView) findViewById(R.id.article_heading);`

---

## 3. Values Resource Files Deep-Dive (Exam Core Topic)

### A. Strings (`res/values/strings.xml`)
Encapsulates application text strings for localization.

```xml
<resources>
    <string name="app_name">Hello Toast</string>
    <string name="button_label_count">Count</string>
    <string name="article_title">Mobile Application Development</string>
</resources>
```

#### String Formatting & Special Character Escaping Rules:
- **Apostrophes & Quotes**: Must be escaped with a backslash (`\'` or `\"`).
- **Line Breaks**: Use `\n` to introduce paragraph line breaks.
- **Embedded HTML Formatting Tags**: `TextView` parses basic HTML tags inside string resources:
  - Bold: `<b>bold text</b>`
  - Italics: `<i>italic text</i>`
  - Combined: `<b><i>bold and italic</i></b>`
- **String Extraction Shortcut**: Select hardcoded string in XML editor $\rightarrow$ Press `Alt + Enter` (Windows) / `Option + Return` (Mac) $\rightarrow$ Select **Extract string resource**.

---

### B. Colors (`res/values/colors.xml`)
Defines color constants using **Hexadecimal ARGB (Alpha-Red-Green-Blue)** format.

```xml
<resources>
    <color name="colorPrimary">#3F51B5</color>
    <color name="colorPrimaryDark">#303F9F</color>
    <color name="colorAccent">#FF4081</color>
    <color name="myBackgroundColor">#FFF043</color>
</resources>
```
- **Hex Structure**: Starts with `#` followed by 6 digits (`#RRGGBB`) or 8 digits including transparency Alpha channel (`#AARRGGBB`). (e.g., `#000000` = Black, `#559FE3` = Sky Blue).

---

### C. Dimensions (`res/values/dimens.xml`)
Stores physical dimensions, margins, paddings, and font sizes to ensure consistent spacing across screens.

```xml
<resources>
    <dimen name="activity_horizontal_margin">16dp</dimen>
    <dimen name="line_spacing">4dp</dimen>
    <dimen name="count_text_size">160sp</dimen>
</resources>
```

#### Units of Measurement (Exam 6-Mark Question):
1. **`dp` (Density-Independent Pixels)**:
   - Abstract unit based on physical screen density.
   - $1\text{dp}$ equals $1\text{px}$ on a $160\text{ dpi}$ baseline screen (MDPI).
   - Used for all layout widths, heights, margins, and paddings. Scales automatically across different screen densities.
2. **`sp` (Scale-Independent Pixels)**:
   - Like `dp`, but scaled according to user preference settings for font size.
   - **Mandatory for all text sizes** (`textSize`) to respect user accessibility settings.
3. **`px` (Pixels)**: Fixed physical screen pixels. Discouraged due to varying screen densities.

---

## 4. Text Views (`TextView` & `EditText`)

`TextView` is a direct subclass of `View` designed to display formatted text content on screen.

### Common `TextView` XML Attributes:
- `android:text`: Sets displayed string (`@string/hello_world`).
- `android:textColor`: Sets text color (`@color/colorPrimary`).
- `android:textSize`: Sets font size in `sp` (`20sp`).
- `android:textStyle`: Modifies style (`normal`, `bold`, `italic`, `bold|italic`).
- `android:typeface`: Sets font family (`normal`, `sans`, `serif`, `monospace`).
- `android:lineSpacingExtra`: Adds extra spacing between text lines in `dp` or `sp`.
- `android:autoLink`: Automatically detects and turns text patterns into touchable hyperlinks:
  - `web` (URLs) | `email` (Email addresses) | `phone` (Phone numbers) | `map` (Addresses) | `all` (Every pattern). Example: `android:autoLink="web"`.

---

## 5. Scrolling Views: `ScrollView` vs. `HorizontalScrollView`

When content exceeds device screen bounds, a scrolling container must be used.

### `ScrollView` Principles & Constraints:
1. `ScrollView` is a subclass of `FrameLayout`.
2. **Single Child Constraint**: A `ScrollView` can contain **exactly ONE direct child View or ViewGroup**.
3. To scroll multiple elements (e.g., heading + subheading + body text), wrap those child views inside a single vertical `LinearLayout` container placed inside the `ScrollView`.

---

## 6. `ScrollView` Performance Limitations vs. `RecyclerView` (Exam Deep-Dive Question)

### Architectural & Memory Comparison Matrix

| Technical Feature | `ScrollView` | `RecyclerView` |
| :--- | :--- | :--- |
| **View Creation & Memory** | Loads **ALL** child views into memory and inflates them into the view hierarchy simultaneously, even if off-screen. | Loads **ONLY** currently visible items on screen plus a minimal buffer. |
| **View Recycling** | **No view recycling**. Views off-screen remain active in RAM. | **Dynamic View Recycling**. Reuses off-screen `ViewHolder` objects as user scrolls. |
| **Performance Impact** | Consumes heavy RAM/CPU for long lists or images, causing UI stutter/lag ("jank"). | Lightweight and memory efficient; designed specifically for massive datasets and image lists. |
| **Best Use Case** | Smoothly scrolling short, fixed-length static pages (e.g., magazine articles, terms of service). | Long dynamic data lists (e.g., news feeds, contacts list, chat history). |

### Performance Optimization Guidelines for `ScrollView`:
- Do **NOT** place bitmap images inside a `ScrollView`.
- Avoid deep nested `LinearLayout` view hierarchies inside `ScrollView` (especially using `android:layout_weight`, which forces dual measurement passes).

---

## 7. Complete Exam XML Example: Scrollable Article Layout (`activity_main.xml`)

Below is a complete, production-grade XML layout featuring a fixed heading with a vertically scrollable body (`ScrollView` enclosing a vertical `LinearLayout`).

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".MainActivity">

    <!-- Fixed Article Main Heading (Non-Scrolling) -->
    <TextView
        android:id="@+id/article_heading"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/article_title"
        android:textAppearance="@android:style/TextAppearance.Large"
        android:textStyle="bold"
        android:textColor="@color/colorPrimary" />

    <!-- Vertical Scroll View Container (Positioned Below Heading) -->
    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/article_heading"
        android:layout_marginTop="8dp">

        <!-- Single Direct Child: Vertical LinearLayout -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <!-- Subheading inside ScrollView -->
            <TextView
                android:id="@+id/article_subheading"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:padding="@dimen/line_spacing"
                android:text="@string/article_subtitle"
                android:textAppearance="@android:style/TextAppearance.Medium"
                android:textStyle="italic" />

            <!-- Long Body Text with autoLink and extra line spacing -->
            <TextView
                android:id="@+id/article_body"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:autoLink="web"
                android:lineSpacingExtra="@dimen/line_spacing"
                android:text="@string/article_text"
                android:textSize="16sp" />

        </LinearLayout>
    </ScrollView>

</RelativeLayout>
```
