# Menus & Navigation Controls in Android

---

## 1. Overview & The 4 Types of Android Menus (Exam Core Matrix)

A **Menu** is a user interface component present in activities that provides a set of actionable options (searching, editing, saving, settings, or screen navigation).

```
                              TYPES OF MENUS IN ANDROID
                                         |
     +-------------------+---------------+---------------+-------------------+
     |                   |                               |                   |
     v                   v                               v                   v
OPTIONS MENU       CONTEXT MENU              CONTEXTUAL ACTION BAR      POPUP MENU
(App Bar top)      (Floating list on         (Temporary top bar on      (Anchored to specific
                   Long-Click)               Long-Click)                View/Button)
```

| Menu Type | Trigger / Gesture | Typical Location | Common Use Cases |
| :--- | :--- | :--- | :--- |
| **Options Menu** | Displays directly in the **App Bar (Action Bar)** or via Overflow button (`⋮`). | Top right corner of the screen. | Global actions affecting the entire app (Search, Settings, Help, Profile). |
| **Context Menu** | **Long-press / Long-click** (`onLongClick`) on a specific View or list item. | Floating modal dialog box in the center of the screen. | Actions affecting the specific element (Edit, Delete, Copy, Share). |
| **Contextual Action Bar (CAB)** | **Long-press / Long-click** on one or more items (e.g., in a list). | Temporarily overlays the top App Bar with a contextual action bar. | Multi-selection batch actions (Delete 5 emails, Share 3 photos). |
| **Popup Menu** | Explicit user tap/click on a specific View (e.g., `ImageButton`). | Anchored directly adjacent to the clicked View. | Overflow dropdown for a specific UI sub-component (e.g., Reply/Reply All in Gmail). |

---

## 2. The Options Menu & App Bar (`Toolbar`)

### A. Setting up `Toolbar` as the App Bar (Support Library)
Android recommends using `android.support.v7.widget.Toolbar` from the Support Library instead of the legacy native `ActionBar`.

1. Set the Activity theme to a `NoActionBar` variant in `styles.xml`:
   ```xml
   <style name="AppTheme.NoActionBar">
       <item name="windowActionBar">false</item>
       <item name="windowNoTitle">true</item>
   </style>
   ```
2. Add `AppBarLayout` and `Toolbar` in the layout (`activity_main.xml` inside `CoordinatorLayout`):
   ```xml
   <android.support.design.widget.AppBarLayout
       android:layout_width="match_parent"
       android:layout_height="wrap_content">

       <android.support.v7.widget.Toolbar
           android:id="@+id/toolbar"
           android:layout_width="match_parent"
           android:layout_height="?attr/actionBarSize"
           android:background="?attr/colorPrimary"
           app:popupTheme="@style/AppTheme.PopupOverlay" />
   </android.support.design.widget.AppBarLayout>
   ```
3. Set the toolbar as the Action Bar in `MainActivity.java`:
   ```java
   Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
   setSupportActionBar(toolbar);
   ```

---

### B. Defining Options Menu XML (`res/menu/menu_main.xml`)

Key attributes inside `<item>`:
- **`android:id`**: Unique resource identifier for the item.
- **`android:title`**: String resource title for the menu item.
- **`android:icon`**: Drawable icon resource.
- **`android:orderInCategory`**: Integer sorting order (lowest numbers appear first).
- **`app:showAsAction`** (Exam 4-Mark Attribute breakdown):
  - `"always"`: Always force item to appear as an icon in the App Bar.
  - `"ifRoom"`: Displays as an icon in App Bar if screen space permits; otherwise pushes into overflow (`⋮`).
  - `"never"`: Never place in App Bar; always list inside overflow dropdown menu.
  - `"withText"`: Include title text alongside the icon.

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/action_search"
        android:icon="@drawable/ic_search"
        android:orderInCategory="10"
        android:title="@string/action_search"
        app:showAsAction="ifRoom" />

    <item
        android:id="@+id/action_favorites"
        android:icon="@drawable/ic_favorite"
        android:orderInCategory="20"
        android:title="@string/action_favorites"
        app:showAsAction="ifRoom" />

    <item
        android:id="@+id/action_settings"
        android:orderInCategory="100"
        android:title="@string/action_settings"
        app:showAsAction="never" />
</menu>
```

---

### C. Inflating & Handling Options Menu Clicks in Java

```java
// 1. Inflate the XML menu resource into the Menu object
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.menu_main, menu);
    return true; // Return true to display the menu
}

// 2. Handle item selection
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.action_search:
            openSearch();
            return true;
        case R.id.action_favorites:
            openFavorites();
            return true;
        case R.id.action_settings:
            openSettings();
            return true;
        default:
            return super.onOptionsItemSelected(item);
    }
}
```

---

## 3. Context Menu (Floating Context Menu)

A **Floating Context Menu** appears as a modal list when a registered View is **long-pressed**.

### 3 Steps to Implement Floating Context Menu:

```java
// Step 1: Register target View in onCreate()
TextView articleTextView = (TextView) findViewById(R.id.article_text);
registerForContextMenu(articleTextView);

// Step 2: Create/Inflate Context Menu
@Override
public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
    super.onCreateContextMenu(menu, v, menuInfo);
    MenuInflater inflater = getMenuInflater();
    inflater.inflate(R.menu.menu_context, menu);
}

// Step 3: Handle Context Menu Item Clicks
@Override
public boolean onContextItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.context_edit:
            editArticle();
            return true;
        case R.id.context_delete:
            deleteArticle();
            return true;
        case R.id.context_share:
            shareArticle();
            return true;
        default:
            return super.onContextItemSelected(item);
    }
}
```

---

## 4. Contextual Action Mode (Contextual Action Bar - CAB)

Unlike floating context menus, the **Contextual Action Bar (CAB)** temporarily replaces the top App Bar with contextual action icons while items are selected (ideal for multi-item selection).

```java
// Trigger CAB on Long Click
myView.setOnLongClickListener(new View.OnLongClickListener() {
    @Override
    public boolean onLongClick(View v) {
        if (mActionMode != null) return false;
        // Start Contextual Action Mode
        mActionMode = startSupportActionMode(mActionModeCallback);
        v.setSelected(true);
        return true;
    }
});

// Callback Interface Definition
private ActionMode.Callback mActionModeCallback = new ActionMode.Callback() {
    @Override
    public boolean onCreateActionMode(ActionMode mode, Menu menu) {
        MenuInflater inflater = mode.getMenuInflater();
        inflater.inflate(R.menu.menu_contextual, menu);
        return true;
    }

    @Override
    public boolean onPrepareActionMode(ActionMode mode, Menu menu) {
        return false;
    }

    @Override
    public boolean onActionItemClicked(ActionMode mode, MenuItem item) {
        switch (item.getItemId()) {
            case R.id.action_delete:
                deleteSelectedItem();
                mode.finish(); // Action picked, close CAB
                return true;
            default:
                return false;
        }
    }

    @Override
    public void onDestroyActionMode(ActionMode mode) {
        mActionMode = null; // Clean up mode variable
    }
};
```

---

## 5. Popup Menu (`PopupMenu`)

A **`PopupMenu`** anchors a modal dropdown menu directly to a specific `View` (e.g., an overflow `ImageButton` inside a card item).

```java
public void showPopupMenu(View view) {
    // Instantiate PopupMenu anchored to the clicked View parameter
    PopupMenu popup = new PopupMenu(MainActivity.this, view);
    
    // Inflate menu XML
    popup.getMenuInflater().inflate(R.menu.menu_popup, popup.getMenu());

    // Set Menu Item Click Listener
    popup.setOnMenuItemClickListener(new PopupMenu.OnMenuItemClickListener() {
        @Override
        public boolean onMenuItemClick(MenuItem item) {
            switch (item.getItemId()) {
                case R.id.popup_reply:
                    replyMessage();
                    return true;
                case R.id.popup_forward:
                    forwardMessage();
                    return true;
                default:
                    return false;
            }
        }
    });

    // Show popup
    popup.show();
}
```
