# Automated UI Testing with Espresso & UI Automator

---

## 1. Overview: Manual vs. Automated UI Testing

**UI Testing** focuses on validating user interactions, visual layouts, data representations, and input control behaviors across different application states and device configurations.

```
                                  UI TESTING METHODS
                                          |
                   +----------------------+----------------------+
                   |                                             |
                   v                                             v
            MANUAL TESTING                               AUTOMATED TESTING
     (Human tester taps buttons)                     (Programmatic script execution)
                   |                                             |
     +-------------+-------------+                 +-------------+-------------+
     |                           |                 |                           |
     v                           v                 v                           v
Domain Size Flaw           Sequence Flaw      Single-App Testing          Cross-App Testing
(Too many permutations)   (Complex multi-   (Espresso Framework)        (UI Automator Framework)
                          step flows)
```

---

### Manual Testing Flaws (Exam 4-Mark Core Question):

1. **Domain Size Flaw**: Even small apps have hundreds of UI view states and permutations. Manual testing of all screen density, orientation, and input edge-case combinations is tedious, time-consuming, and error-prone.
2. **Sequence Flaw**: Complex user journeys require exact sequential steps (e.g. open camera $\rightarrow$ capture photo $\rightarrow$ crop image $\rightarrow$ attach to message $\rightarrow$ send). High operation counts exponentially increase testing sequence complexity.

---

## 2. Espresso vs. UI Automator (Exam 6-Mark Comparison Matrix)

Android provides two core UI testing frameworks inside the **Android Testing Support Library (ATSL)**:

| Dimension | Espresso | UI Automator |
| :--- | :--- | :--- |
| **Scope / Target** | **Single-App Testing**: Intra-app UI interactions within the target application. | **Cross-App Testing**: Inter-app UI interactions spanning multiple apps or system settings. |
| **Android Version** | Android 2.2 (API Level 8) and higher. | Android 4.3 (API Level 18) and higher. |
| **Synchronization** | **Automatic Thread Synchronization**: Detects when main UI thread is idle before executing test steps. | Manual timeouts or explicit waiting condition hooks (`Until.findObject()`). |
| **Key Use Cases** | Button clicks, text entry, list scrolling, view assertions within your app. | Opening device Settings, picking contacts from native contacts app, push notifications. |

---

## 3. What is Android Instrumentation? (Exam 4-Mark Concept)

**Instrumentation** is a set of control hooks built into the Android system that loads the test package and the target application into the **same system process**.

```
+-----------------------------------------------------------------+
|                   SAME SYSTEM PROCESS                           |
|                                                                 |
|   +-----------------------+           +---------------------+   |
|   | Target App Components | <-------> | Instrumentation Test |   |
|   | (Activities/Views)    |           | (Espresso Script)   |   |
|   +-----------------------+           +---------------------+   |
+-----------------------------------------------------------------+
```

- **Why it matters**: Normally, Android component lifecycles (`onCreate`, `onResume`, `onPause`) are managed strictly by the OS. Instrumentation allows test runners (`AndroidJUnitRunner`) to directly control, invoke, and monitor lifecycle callbacks and inspect private view fields.

---

## 4. Test Environment Setup & Annotations

### Project Location:
Instrumented tests MUST be stored in the module's `src/androidTest/java/` directory.

### Dependencies in `build.gradle (Module: app)`:
```groovy
defaultConfig {
    // Specify the test runner
    testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
}

dependencies {
    // JUnit 4 framework
    testCompile 'junit:junit:4.12'

    // Espresso Core library
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
}
```

---

### Core JUnit 4 & Android Test Annotations Matrix:

| Annotation | Purpose & Function |
| :--- | :--- |
| **`@RunWith(AndroidJUnit4.class)`** | Specifies `AndroidJUnit4` as the primary test runner class to execute tests. |
| **`@Rule`** | Establishes testing context before running tests. `ActivityTestRule<MainActivity>` automatically launches `MainActivity` before each test. |
| **`@Test`** | Demarcates a single executable test method. |
| **`@Before`** | Code block executed **before** every `@Test` method (used for initial state setup). |
| **`@After`** | Code block executed **after** every `@Test` method (used for cleanup). |
| **`@SmallTest` / `@MediumTest` / `@LargeTest`** | Classifies resource requirements (Small = local memory/no DB, Medium = local DB/file system, Large = network/external systems). |

---

## 5. Writing Espresso Tests: ViewMatchers, ViewActions, and ViewAssertions

An Espresso test follows a simple **3-Step Architectural Formula**:

$$\text{Match View} \longrightarrow \text{Perform Action} \longrightarrow \text{Assert Result}$$

```
onView(ViewMatcher)  --->  .perform(ViewAction)  --->  .check(ViewAssertion)
```

```java
// Complete Espresso Formula Statement:
onView(withId(R.id.my_button))           // 1. ViewMatcher (Find View)
    .perform(click())                    // 2. ViewAction  (Interact)
    .check(matches(isDisplayed()));       // 3. ViewAssertion (Verify Result)
```

---

### The 3 Core Espresso Components:

1. **`ViewMatchers`** (Locates views in current View hierarchy):
   - `withId(R.id.id_name)`: Finds view by its XML ID.
   - `withText("Text Content")`: Finds view by exact string.
   - `allOf(withId(...), withText(...))`: Combines multiple conditions using Hamcrest matchers.
2. **`ViewActions`** (Simulates user gestures):
   - `click()`, `typeText("Sample String")`, `clearText()`, `closeSoftKeyboard()`, `swipeLeft()`, `scrollTo()`.
3. **`ViewAssertions`** (Verifies view state):
   - `.check(matches(isDisplayed()))`
   - `.check(matches(withText("Expected Output")))`

---

## 6. Testing `AdapterView` Components (Spinners & Lists) with `onData()`

### Why `onView()` Fails on Adapters:
In dynamic views like `Spinner`, `ListView`, or `GridView`, child views are loaded dynamically at runtime as the user scrolls. Items currently off-screen do **not** exist in the view hierarchy, causing `onView()` to throw a `NoMatchingViewException`.

### The `onData()` Solution:
`onData()` uses a `DataInteraction` object to query the raw **Adapter dataset**, automatically scrolling to and loading the target view into focus before performing actions.

```java
@RunWith(AndroidJUnit4.class)
public class SpinnerSelectionTest {

    @Rule
    public ActivityTestRule<MainActivity> mActivityRule = 
            new ActivityTestRule<>(MainActivity.class);

    @Test
    public void testSpinnerSelection() {
        // 1. Click Spinner view to open dropdown list
        onView(withId(R.id.label_spinner)).perform(click());

        // 2. Use onData() to locate String item in dataset and click it
        onData(allOf(is(instanceOf(String.class)), is("Americano"))).perform(click());

        // 3. Click submit button
        onView(withId(R.id.button_main)).perform(click());

        // 4. Assert TextView contains selected spinner text
        onView(withId(R.id.text_phonelabel))
            .check(matches(withText(containsString("Americano"))));
    }
}
```

---

## 7. Espresso Test Recorder in Android Studio

Android Studio provides a built-in tool called **Espresso Test Recorder**:
- **Location**: `Run` $\rightarrow$ `Record Espresso Test`.
- **Function**: Launches app on emulator/device, records user clicks and typing gestures in real time, and automatically outputs a clean Java JUnit test file in `src/androidTest/java/`.
