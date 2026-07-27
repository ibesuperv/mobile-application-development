# Material Design Specifications, Components & Motion

---

## 1. Introduction & 4 Core Principles of Material Design (Exam Core Concept)

**Material Design** is a visual design language and philosophy created by Google in 2014 to synthesize classic principles of good design with technology and innovation, ensuring a unified user experience across mobile, tablet, desktop, and wearable platforms.

### The 4 Core Principles of Material Design (Exam 8-Mark Question):

1. **The Material Metaphor**:
   - UI elements behave like physical, real-world paper material.
   - Elements occupy 3D space, cast realistic drop shadows based on elevation ($Z$-axis), and interact tactilely with one another without passing through each other.
2. **Bold, Graphic, Intentional**:
   - Employs deliberate grid alignments, edge-to-edge structural layouts, intentional white space, and large-scale typography.
   - Uses contrasting color palettes to draw immediate focus to primary user actions (e.g., Floating Action Buttons).
3. **Meaningful Motion**:
   - Motion is non-random, responsive, and natural.
   - Animations reinforce user agency (actions are initiated by touch contact), guide user focus during context shifts, and respect real-world physics (curved arcs under gravity via `PathInterpolator`).
4. **Adaptive & Accessible**:
   - Responsive across varying display sizes, orientations, and pixel densities.
   - Ensures high color contrast ratios, dynamic scaling (`sp`), and minimum touch target sizes of **48dp x 48dp**.

---

## 2. Material Design Color System

Material Design utilizes a structured color palette. Standard colors are assigned numerical shade weights ranging from **50** (lightest tint) to **900** (darkest shade), with **500** as the primary baseline tint. Accent colors begin with **A** (e.g., `A200`).

### Standard Color Attributes in `res/values/colors.xml` & `styles.xml`:

```xml
<!-- res/values/colors.xml -->
<resources>
    <!-- Primary Brand Color (500 shade used for App Bar / Toolbar) -->
    <color name="colorPrimary">#3F51B5</color>
    
    <!-- Darker Primary Shade (700 shade used for top System Status Bar) -->
    <color name="colorPrimaryDark">#303F9F</color>
    
    <!-- Contrasting Accent Color (used for FAB, active switches, checkboxes) -->
    <color name="colorAccent">#FF4081</color>
</resources>
```

```
+-------------------------------------------------------+
|  Status Bar  : colorPrimaryDark                       |
+-------------------------------------------------------+
|  App Bar     : colorPrimary                           |
|                                                       |
|  Content     : Light / Dark Background                |
|                [ FAB : colorAccent ]                  |
+-------------------------------------------------------+
```

---

## 3. Essential Material Design Components (Exam 10-Mark Breakdown)

---

### A. Floating Action Button (`FloatingActionButton` / FAB)
A circular, elevated button that represents the **single primary action** on a screen (e.g., "Compose" in Gmail, "Add Contact" in Contacts).

```xml
<android.support.design.widget.FloatingActionButton
    android:id="@+id/fab"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|end"
    android:layout_margin="16dp"
    android:src="@drawable/ic_add"
    app:backgroundTint="@color/colorAccent"
    app:elevation="6dp"
    app:fabSize="normal" />
```
- **Sizes**: `normal` (56dp x 56dp) or `mini` (40dp x 40dp).
- **Elevation**: Defaults to `6dp` resting, lifts to `12dp` when pressed.

---

### B. Snackbars (`Snackbar`)
Provides brief, lightweight feedback about an operation in a horizontal bar at the bottom of the screen.
- **Snackbar vs. Toast (4-Mark Core Distinction)**:

| Feature | Snackbar | Toast |
| :--- | :--- | :--- |
| **User Action** | Can include an interactive text action button (e.g., **"UNDO"**). | Text-only feedback; no interactive buttons. |
| **Swipe Dismissal** | Can be swiped off screen manually by the user (when inside `CoordinatorLayout`). | Cannot be swiped off screen; disappears strictly on timeout. |
| **Layout Awareness** | Lifts `FloatingActionButton` up automatically so it isn't covered. | Draws on top of UI elements indiscriminately. |

```java
// Displaying a Snackbar with an UNDO action
Snackbar.make(findViewById(R.id.myCoordinatorLayout), R.string.email_deleted, Snackbar.LENGTH_LONG)
    .setAction("UNDO", new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            // Restore deleted item
        }
    })
    .show();
```

---

### C. Cards (`CardView`)
A sheet of material that serves as an entry point to detailed information. Each card represents **one single subject**.

```xml
<android.support.v7.widget.CardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="8dp"
    app:cardElevation="4dp"
    app:cardUseCompatPadding="true">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Card Title"
            android:textSize="18sp"
            android:textStyle="bold" />
    </LinearLayout>

</android.support.v7.widget.CardView>
```

---

## 4. Material Motion & Animations (Exam Core Question)

Motion in Material Design is categorized into 4 characteristics: **Responsive**, **Natural**, **Aware**, and **Intentional**.

### A. Touch Feedback & Ripple Effects (`RippleDrawable`)
Touch feedback provides instant visual confirmation at the point of touch contact.

```xml
<!-- Bounded Ripple (contained within View boundary) -->
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="?android:attr/selectableItemBackground" />

<!-- Unbounded Borderless Ripple (extends beyond View boundary) -->
<ImageButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="?android:attr/selectableItemBackgroundBorderless" />
```

---

### B. Circular Reveal Animation (`ViewAnimationUtils`)
Shows or hides a group of UI elements by expanding or shrinking a clipping circle.

```java
// Reveal a previously INVISIBLE View using Circular Reveal
View myView = findViewById(R.id.my_view);

int cx = myView.getWidth() / 2;
int cy = myView.getHeight() / 2;
float finalRadius = (float) Math.hypot(cx, cy);

// Create circular reveal animator (start radius = 0, end radius = hypotenuse)
Animator anim = ViewAnimationUtils.createCircularReveal(myView, cx, cy, 0, finalRadius);

myView.setVisibility(View.VISIBLE);
anim.start();
```

---

### C. Activity Window Transitions (Explode, Slide, Fade, Shared Element)

Activity transitions provide visual continuity when switching screens.

#### 1. Transition Types:
- **Enter / Exit Transitions**: `Explode` (views fly in/out from center), `Slide`, `Fade`.
- **Shared Element Transitions**: Smoothly translates and scales a shared View (e.g. thumbnail image in list $\rightarrow$ full image in detail view).

#### 2. Enabling Activity Transitions in XML Theme (`styles.xml`):
```xml
<style name="BaseAppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="android:windowActivityTransitions">true</item>
    <item name="android:windowEnterTransition">@transition/explode</item>
    <item name="android:windowExitTransition">@transition/explode</item>
    <item name="android:windowSharedElementEnterTransition">@transition/change_image_transform</item>
</style>
```

#### 3. Executing Shared Element Transition in Java:
```java
Intent intent = new Intent(this, DetailActivity.class);

// Pass shared ImageView component between activities
ActivityOptionsCompat options = ActivityOptionsCompat.makeSceneTransitionAnimation(
    MainActivity.this,
    sharedImageView,
    "profile_transform"
);

startActivity(intent, options.toBundle());
```

---

### D. Curved Motion (`PathInterpolator`)
In real-world physics, objects accelerate along curved arcs rather than linear straight lines. `PathInterpolator` uses Bézier curves to animate position attributes (`View.X`, `View.Y`).

```java
Path path = new Path();
path.moveTo(0, 0);
path.lineTo(0, 300);
path.cubicTo(100, 300, 300, 100, 500, 500);

ObjectAnimator animator = ObjectAnimator.ofFloat(view, View.X, View.Y, path);
animator.setInterpolator(new PathInterpolator(0.4f, 0f, 0.2f, 1f));
animator.start();
```
