# Screen Navigation Patterns & Controls

---

## 1. Overview of Screen Navigation Types (Exam Core Question)

Navigation is the architecture that allows users to traverse across, deep into, and back out from different sections of an application.

```
                           SCREEN NAVIGATION PATTERNS
                                       |
    +----------------------------------+----------------------------------+
    |                                  |                                  |
    v                                  v                                  v
ANCESTRAL / UP                 DESCENDANT NAVIGATION              LATERAL NAVIGATION
NAVIGATION                     (Parent -> Child /                 (Sibling <-> Sibling)
(Child -> Parent)              Navigation Drawer)                 (Tabs / Swipe Views)
```

| Navigation Type | Direction | Target Destination | Primary Controls / Patterns |
| :--- | :--- | :--- | :--- |
| **Back Navigation** | Temporal (Historical) | Previous screen in the Back Stack. | Hardware/Software **Back Button** (`onBackPressed()`). |
| **Ancestral (Up) Navigation** | Vertical Upwards | Parent screen in the app's structural hierarchy. | **Up Button (`←`)** in App Bar. |
| **Descendant Navigation** | Vertical Downwards | Child screen or detailed view. | Dashboards, Buttons, **Navigation Drawer**. |
| **Lateral Navigation** | Horizontal (Same Level) | Sibling screens at the same structural level. | **Tabs (`TabLayout`)**, **Swipe Views (`ViewPager`)**. |

---

## 2. Temporal vs. Ancestral Navigation: Back Button vs. Up Button (Core Exam 6-Mark Question)

```
[Launcher] ──> [Parent Screen: MainActivity] ──> [Child Screen: DetailActivity]
                                                   │                │
                                      (Up Button)  │                │ (Back Button)
                                      Goes to      v                v  Goes to
                                  [MainActivity]            [Previous Back Stack State]
```

### The Crucial Differences:

| Dimension | Back Button | Up Button (`←`) |
| :--- | :--- | :--- |
| **Location** | System navigation bar at the bottom of the device (or system back gesture). | Left edge of the top **App Bar / Toolbar**. |
| **Navigation Basis** | **Temporal (History)**: Navigates backward through the user's historical Back Stack of screens in reverse chronological order. | **Hierarchical (Structural)**: Navigates upwards strictly to the declared **Parent Activity** in the app screen hierarchy. |
| **Cross-App Behavior** | Can take the user completely out of the app back to another app or home screen if that's where they came from. | Always stays **within the current app**, taking the user to the designated parent activity. |
| **Implementation** | Handled automatically by system back stack. Can override `onBackPressed()`. | Requires declaring `android:parentActivityName` in `AndroidManifest.xml`. |

---

### Implementing Up Navigation in `AndroidManifest.xml` & Java

#### 1. Declare Parent Activity in Manifest:
```xml
<activity
    android:name=".DetailActivity"
    android:label="@string/title_detail"
    android:parentActivityName=".MainActivity">
    
    <!-- Backward compatibility meta-data for API < 16 -->
    <meta-data
        android:name="android.support.PARENT_ACTIVITY"
        android:value=".MainActivity" />
</activity>
```

#### 2. Enable Up Button in Child Activity Java Code:
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_detail);

    Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
    setSupportActionBar(toolbar);

    // Enable the Up button in the App Bar
    if (getSupportActionBar() != null) {
        getSupportActionBar().setDisplayHomeAsUpEnabled(true);
    }
}
```

---

## 3. Descendant Navigation: Navigation Drawer (`DrawerLayout`)

A **Navigation Drawer** is a slide-in panel displaying primary navigation destinations on the left edge of the screen.

### Anatomical Structure of a Navigation Drawer:
1. **`DrawerLayout`**: Root view group managing the sliding drawer panel over the content.
2. **Main Content View (`CoordinatorLayout` / `RelativeLayout`)**: Primary screen UI visible when the drawer is closed.
3. **`NavigationView`**: The sliding drawer containing a **Header Layout** (`app:headerLayout`) and a **Menu Resource** (`app:menu`).

```xml
<!-- activity_main.xml -->
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <!-- 1. Main Content Layout Sibling -->
    <include
        layout="@layout/app_bar_main"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <!-- 2. Sliding Navigation Drawer -->
    <android.support.design.widget.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        app:headerLayout="@layout/nav_header_main"
        app:menu="@menu/activity_main_drawer" />

</android.support.v4.widget.DrawerLayout>
```

---

### Java Setup with `ActionBarDrawerToggle` & `OnNavigationItemSelectedListener`

```java
public class MainActivity extends AppCompatActivity 
        implements NavigationView.OnNavigationItemSelectedListener {

    private DrawerLayout mDrawer;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);

        mDrawer = (DrawerLayout) findViewById(R.id.drawer_layout);
        
        // Connect Toolbar to DrawerLayout with hamburger icon animation
        ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(
            this, mDrawer, toolbar, R.string.navigation_drawer_open, R.string.navigation_drawer_close);
        mDrawer.addDrawerListener(toggle);
        toggle.syncState();

        NavigationView navigationView = (NavigationView) findViewById(R.id.nav_view);
        navigationView.setNavigationItemSelectedListener(this);
    }

    @Override
    public boolean onNavigationItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.nav_camera:
                // Handle camera selection
                break;
            case R.id.nav_gallery:
                // Handle gallery selection
                break;
        }

        // Close drawer after selection
        mDrawer.closeDrawer(GravityCompat.START);
        return true;
    }
}
```

---

## 4. Lateral Navigation: Tabs & Swipe Views (`TabLayout` + `ViewPager`)

**Lateral Navigation** allows switching between sibling screens at the same level using horizontal touch swipe gestures or top tab clicks.

### Components Needed for Swipe Views with Tabs (Exam 8-Mark Question):
1. **`TabLayout`**: Displays horizontal tab bars (`android.support.design.widget.TabLayout`).
2. **`ViewPager`**: Layout manager allowing users to flip left and right through pages of data (`android.support.v4.view.ViewPager`).
3. **`PagerAdapter` / `FragmentPagerAdapter`**: Controller populating `ViewPager` with `Fragment` screens.

---

### Complete Executable Code Pattern: Tabs + ViewPager

#### 1. Activity XML Layout (`activity_main.xml`):
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="?attr/colorPrimary" />

    <!-- TabLayout for top tab bar -->
    <android.support.design.widget.TabLayout
        android:id="@+id/tab_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="?attr/colorPrimary"
        app:tabMode="fixed"
        app:tabGravity="fill" />

    <!-- ViewPager for horizontal page swipe -->
    <android.support.v4.view.ViewPager
        android:id="@+id/pager"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</LinearLayout>
```

#### 2. FragmentPagerAdapter Java Implementation (`PagerAdapter.java`):
```java
public class PagerAdapter extends FragmentPagerAdapter {
    int mNumOfTabs;

    public PagerAdapter(FragmentManager fm, int NumOfTabs) {
        super(fm);
        this.mNumOfTabs = NumOfTabs;
    }

    @Override
    public Fragment getItem(int position) {
        switch (position) {
            case 0:
                return new TabFragment1();
            case 1:
                return new TabFragment2();
            case 2:
                return new TabFragment3();
            default:
                return null;
        }
    }

    @Override
    public int getCount() {
        return mNumOfTabs;
    }
}
```

#### 3. Activity Binding Java Code (`MainActivity.java`):
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);

        // Configure Tabs
        TabLayout tabLayout = (TabLayout) findViewById(R.id.tab_layout);
        tabLayout.addTab(tabLayout.newTab().setText("Top Stories"));
        tabLayout.addTab(tabLayout.newTab().setText("Tech"));
        tabLayout.addTab(tabLayout.newTab().setText("Entertainment"));

        final ViewPager viewPager = (ViewPager) findViewById(R.id.pager);
        final PagerAdapter adapter = new PagerAdapter(getSupportFragmentManager(), tabLayout.getTabCount());
        viewPager.setAdapter(adapter);

        // Connect ViewPager swipe events to TabLayout selection
        viewPager.addOnPageChangeListener(new TabLayout.TabLayoutOnPageChangeListener(tabLayout));
        tabLayout.addOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
            @Override
            public void onTabSelected(TabLayout.Tab tab) {
                viewPager.setCurrentItem(tab.getPosition());
            }

            @Override
            public void onTabUnselected(TabLayout.Tab tab) {}

            @Override
            public void onTabReselected(TabLayout.Tab tab) {}
        });
    }
}
```
