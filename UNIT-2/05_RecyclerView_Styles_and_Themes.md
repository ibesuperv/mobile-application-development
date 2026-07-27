# RecyclerView, Drawables, Styles, Themes & Material Design UX

---

## SECTION 1: RecyclerView Architecture (10-Mark Core Question)

`RecyclerView` (from the Support / AndroidX library) is an advanced, flexible, and high-performance replacement for legacy `ListView`. It efficiently displays large, scrollable datasets by **recycling and reusing View items** as they scroll off-screen.

```
                              RECYCLERVIEW ARCHITECTURE
                                          |
          +-------------------------------+-------------------------------+
          |                               |                               |
          v                               v                               v
RECYCLERVIEW WIDGET               LAYOUT MANAGER                       ADAPTER
(Container in Layout)             (Positions Item Views)          (Binds Data to Views)
                                          |                               |
                                          v                               v
                                  - LinearLayoutManager               VIEWHOLDER
                                  - GridLayoutManager           (Caches View References)
                                  - StaggeredGridLayoutManager
```

---

### 1. The 5 Core Components of RecyclerView

1. **`RecyclerView` Widget**: The scrolling container defined in the activity layout file (`<android.support.v7.widget.RecyclerView>`).
2. **Item XML Layout**: A separate XML layout file (`res/layout/wordlist_item.xml`) defining the structure of **one single list item**.
3. **`LayoutManager`**: Positions item views inside the container and dictates layout arrangement:
   - `LinearLayoutManager`: Vertical or horizontal scrolling list.
   - `GridLayoutManager`: Uniform grid layout (e.g., photo gallery).
   - `StaggeredGridLayoutManager`: Staggered grid layout (e.g., Pinterest-style variable height cards).
4. **`Adapter` (`RecyclerView.Adapter`)**: Acts as the middleman binding raw data (ArrayList, Cursor) to item Views.
5. **`ViewHolder` (`RecyclerView.ViewHolder`)**: Holds and caches references to the sub-views of a single list item, eliminating expensive repeated `findViewById()` lookups during scrolling.

---

### 2. Mandatory Adapter Callback Methods (Exam 6-Mark Question)

| Adapter Callback | Trigger & Purpose | Return Value |
| :--- | :--- | :--- |
| **`onCreateViewHolder(ViewGroup parent, int viewType)`** | Called when `RecyclerView` needs a brand-new `ViewHolder` instance. Inflates the item layout XML using `LayoutInflater`. | Returns a new custom `ViewHolder` instance. |
| **`onBindViewHolder(ViewHolder holder, int position)`** | Called by `RecyclerView` to populate/bind data at the specified dataset `position` into the cached views of `holder`. | `void` |
| **`getItemCount()`** | Queries the size of the dataset. | Returns `int` total number of items in dataset. |

---

### 3. Complete Executable Code Pattern: Custom RecyclerView Implementation

#### A. Custom Adapter & ViewHolder (`WordListAdapter.java`):

```java
package com.example.android.recyclerviewdemo;

import android.content.Context;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;
import java.util.LinkedList;

public class WordListAdapter extends RecyclerView.Adapter<WordListAdapter.WordViewHolder> {

    private final LinkedList<String> mWordList;
    private final LayoutInflater mInflater;

    // Constructor initializes dataset and layout inflater
    public WordListAdapter(Context context, LinkedList<String> wordList) {
        mInflater = LayoutInflater.from(context);
        this.mWordList = wordList;
    }

    // 1. Inflate item layout XML and return new ViewHolder
    @Override
    public WordViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View mItemView = mInflater.inflate(R.layout.wordlist_item, parent, false);
        return new WordViewHolder(mItemView, this);
    }

    // 2. Bind dataset value at position to ViewHolder views
    @Override
    public void onBindViewHolder(WordViewHolder holder, int position) {
        String mCurrent = mWordList.get(position);
        holder.wordItemView.setText(mCurrent);
    }

    // 3. Return total item count
    @Override
    public int getItemCount() {
        return mWordList.size();
    }

    // Custom ViewHolder class holding view references and handling click events
    class WordViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener {
        public final TextView wordItemView;
        final WordListAdapter mAdapter;

        public WordViewHolder(View itemView, WordListAdapter adapter) {
            super(itemView);
            wordItemView = (TextView) itemView.findViewById(R.id.word);
            this.mAdapter = adapter;
            itemView.setOnClickListener(this); // Register click listener on item view
        }

        @Override
        public void onClick(View v) {
            int mPosition = getLayoutPosition();
            String element = mWordList.get(mPosition);
            mWordList.set(mPosition, "Clicked! " + element);
            mAdapter.notifyDataSetChanged(); // Notify adapter to re-render updated data
        }
    }
}
```

#### B. Activity Setup (`MainActivity.java`):

```java
public class MainActivity extends AppCompatActivity {

    private final LinkedList<String> mWordList = new LinkedList<>();
    private RecyclerView mRecyclerView;
    private WordListAdapter mAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Populate mock dataset
        for (int i = 0; i < 20; i++) {
            mWordList.addLast("Word " + i);
        }

        // Get RecyclerView handle
        mRecyclerView = (RecyclerView) findViewById(R.id.recyclerview);

        // Instantiate custom Adapter
        mAdapter = new WordListAdapter(this, mWordList);

        // Connect Adapter and LayoutManager to RecyclerView
        mRecyclerView.setAdapter(mAdapter);
        mRecyclerView.setLayoutManager(new LinearLayoutManager(this));
    }
}
```

---

## SECTION 2: Drawables, Styles, and Themes

---

### 1. Drawables (`res/drawable`)
A **Drawable** is a general abstraction for graphic assets that can be drawn to the screen (bitmaps, vector graphics, shape XMLs, layer lists, state lists).

#### Types of Drawables:
- **Bitmap Drawables**: PNG, JPEG, or WEBP image files.
- **Vector Drawables (`<vector>`)**: XML files containing vector path definitions that scale dynamically across screen densities without quality loss or pixelation.
- **Shape Drawables (`<shape>`)**: XML drawables defining geometric shapes (rectangles, ovals, rings), corner radii, solid fill colors (`<solid>`), and borders (`<stroke>`).
- **State List Drawables (`<selector>`)**: XML drawables that automatically change background drawables based on the View's current state (e.g., `state_pressed="true"`, `state_disabled="true"`).

```xml
<!-- res/drawable/button_state_selector.xml -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" android:drawable="@color/colorAccent" />
    <item android:state_focused="true" android:drawable="@color/colorPrimaryDark" />
    <item android:drawable="@color/colorPrimary" />
</selector>
```

---

### 2. Styles vs. Themes (Exam 6-Mark Distinction)

| Dimension | Styles (`style`) | Themes (`android:theme`) |
| :--- | :--- | :--- |
| **Definition** | A collection of visual attributes applied to a **single specific View** in a layout. | A collection of visual attributes applied to an **entire Activity or Application**. |
| **Scope** | Scope is localized to an individual widget (e.g. styling a single `Button` or `TextView`). | Scope is global across all View hierarchies inside the activity/application. |
| **XML Syntax** | Specified using the `style="@style/MyStyle"` attribute on a View tag. | Specified using `android:theme="@style/AppTheme"` in `AndroidManifest.xml`. |

#### A. Defining a Custom Style (`res/values/styles.xml`):
```xml
<resources>
    <!-- Base Application Theme -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

    <!-- Custom View Style inheriting from a parent style -->
    <style name="HeaderTextStyle" parent="Widget.AppCompat.TextView">
        <item name="android:textSize">20sp</item>
        <item name="android:textStyle">bold</item>
        <item name="android:textColor">@color/colorPrimary</item>
        <item name="android:padding">16dp</item>
    </style>
</resources>
```

#### B. Night Mode Support (`Theme.AppCompat.DayNight`):
Inheriting from `Theme.AppCompat.DayNight` enables automatic switching between Light and Dark themes based on system time or device battery saver status.

---

## SECTION 3: Delightful User Experience & Material Design UX

Creating a **Delightful User Experience (UX)** in Android involves adhering to Google's **Material Design Principles**:

1. **Tactile Surfaces & Depth**: Uses virtual paper, elevation (`android:elevation`), and real-time drop shadows to establish visual hierarchy.
2. **Bold, Graphic Intentionality**: Employs deliberate typography, whitespace, edge-to-edge imagery, and harmonious palette colors (`colorPrimary`, `colorAccent`).
3. **Meaningful Motion**: Micro-animations and ripple effects (`?attr/selectableItemBackground`) confirm user touch interactions and guide context transitions.
4. **Adaptive Accessibility**: Ensures high color contrast ratios, dynamic font sizing (`sp`), and touch target sizes of at least **48dp x 48dp**.
