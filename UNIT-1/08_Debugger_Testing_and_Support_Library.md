# Android Debugger, App Testing, and Support Library

---

## SECTION 1: The Android Studio Debugger

Debugging is the systematic process of finding, isolating, and resolving runtime errors, logic bugs, memory leaks, and performance bottlenecks in application code.

### 1. Running the App in Debug Mode vs. Attaching Debugger
Android Studio provides two primary mechanisms to debug applications:
1. **Run in Debug Mode**: Click **Debug** icon (`Shift + F9`). Android Studio compiles the APK, signs it with a debug key (`debug.keystore`), sets `android:debuggable="true"`, installs it on the target device/emulator, and attaches the debugger right from app launch (`onCreate()`).
2. **Attach Debugger to Android Process**: If an app is already running on a hardware device or emulator, select **Run > Attach Debugger to Android Process**. Choose the running process from the *Choose Process* dialog. This allows debugging without reinstalling or restarting the application.

---

### 2. Working with Breakpoints (Exam Core Question)
A **Breakpoint** is an intentional stopping/pausing point placed on a line of code where the debugger pauses normal program execution.

#### A. Breakpoint Types & Operations:
- **Toggle Breakpoint**: Click the left gutter next to a line number (or press `Ctrl + F8` / `Cmd + F8`). A red dot appears in the gutter.
- **Mute Breakpoints**: Disables all set breakpoints temporarily without deleting them or losing custom conditions.
- **Exception Breakpoints**: Pauses execution automatically whenever a specific Java exception (e.g., `NullPointerException`, `IndexOutOfBoundsException`) is thrown anywhere in the app.

#### B. Conditional Breakpoints:
A **Conditional Breakpoint** pauses code execution **ONLY IF** a specified boolean expression evaluates to `true`.
- **Setup**: Right-click a breakpoint red dot $\rightarrow$ Enter a boolean expression in the **Condition** field (e.g., `mCount == 10` or `user == null`).
- **Use Case**: Debugging loops or repetitive events where an error occurs only under specific variable states.

---

### 3. Stepping Through Code (Debugger Navigation Shortcuts)

When execution stops at a breakpoint, use the stepping controls to navigate line-by-line:

| Stepping Action | Keyboard Shortcut | Technical Behavior & Execution Flow |
| :--- | :--- | :--- |
| **Step Over** | `F8` | Executes the current line of code and advances to the next line in the current method. If the line contains a method call, it executes the entire method without entering it. |
| **Step Into** | `F7` | Steps inside the execution of a method call on the current line. Opens the method's Java file and moves the execution pointer to its first line. |
| **Step Out** | `Shift + F8` | Finishes executing the remaining lines of the current method and returns control to the calling method (one level up in the call stack). |
| **Resume Program** | `F9` | Resumes normal execution until the next breakpoint is encountered or the app finishes. |

---

### 4. Inspecting App State: Debugger Panes

1. **Frames View**: Displays the **Execution Call Stack** showing all active classes and methods in reverse chronological order (most recent stack frame on top). Clicking a frame jumps directly to that execution context.
2. **Variables View**: Shows all active variables, primitive values, and object properties present in the current stack frame. Allows modifying variable values live during runtime by right-clicking $\rightarrow$ **Set Value (`F2`)**.
3. **Watches View**: Allows adding custom expressions or specific variables (`+` icon) to monitor continuously across debugging sessions.
4. **Evaluate Expression (`Alt + F8`)**: Opens an interactive window to evaluate complex Java expressions, execute object methods on the fly, or test condition logic using current runtime memory state.

---

### 5. Cleaning Up Debug Code Before Production Release

Before publishing an APK to the Google Play Store, **all debug artifacts must be stripped**:
1. Remove all `Log.v()`, `Log.d()`, and `Log.i()` logging statements.
2. Remove transient `Toast.makeText()` notifications.
3. Ensure `android:debuggable="false"` (or remove the attribute from `<application>` in `AndroidManifest.xml`).
4. Remove method tracing code (`Debug.startMethodTracing()` / `stopMethodTracing()`).

---

## SECTION 2: Testing the Android App

Testing ensures code correctness, robust error handling, and regression prevention as the app evolves.

### 1. The Two Primary Android Testing Types (6-Mark Core Question)

```
                            ANDROID APP TESTING
                                    |
          +-------------------------+-------------------------+
          |                                                   |
          v                                                   v
   LOCAL UNIT TESTS                                   INSTRUMENTED TESTS
   (src/test/java)                                  (src/androidTest/java)
   - Executes on Host JVM                           - Executes on Android Device/Emulator
   - Fast, lightweight                              - Access to Android Framework & Context
   - Framework: JUnit 4, Mockito                   - Framework: Espresso, UIAutomator
```

| Dimension | Local Unit Tests (`test`) | Instrumented Tests (`androidTest`) |
| :--- | :--- | :--- |
| **Directory Path** | `src/test/java/` | `src/androidTest/java/` |
| **Execution Environment** | Runs locally on the development machine's **Host JVM**. | Runs on a physical **Android Device** or **Emulator**. |
| **Execution Speed** | Extremely fast (milliseconds). | Slower (requires APK compilation & installation). |
| **Android API Access** | No native access to Android Framework APIs (`Context`, `View`). Requires mocking (`Mockito`). | Full access to Android Framework APIs, `Context`, and UI Views. |
| **Primary Use Case** | Testing business logic, algorithms, utility classes, and data transformations. | Automated **UI Testing**, Activity navigation flows, user input simulation, and integration tests. |
| **Primary Frameworks** | **JUnit 4**, **Mockito**, **Hamcrest**. | **Espresso**, **UIAutomator**, `AndroidJUnitRunner`. |

---

### 2. Anatomy of a JUnit 4 Unit Test (`src/test/java`)

Unit tests verify the smallest unit of code (a single method or class) in isolation.

#### Common JUnit 4 Annotations:
- **`@RunWith(JUnit4.class)`**: Specifies the test runner class used to execute tests in this class.
- **`@SmallTest` / `@MediumTest` / `@LargeTest`**: Categorizes tests by execution duration and scope.
- **`@Before`**: Executes setup logic before **each** individual test method run (e.g., initializing objects).
- **`@After`**: Executes cleanup logic after **each** individual test method run.
- **`@Test`**: Marks a method as an executable test case.

#### Unit Test Example using JUnit 4 & Hamcrest Matchers (`CalculatorTest.java`)

```java
package com.example.android.SimpleCalc;

import test.suite.SmallTest;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.JUnit4;

import static org.hamcrest.CoreMatchers.equalTo;
import static org.hamcrest.CoreMatchers.is;
import static org.junit.Assert.assertThat;

@RunWith(JUnit4.class)
@SmallTest
public class CalculatorTest {

    private Calculator mCalculator;

    // Set up test environment before each @Test method
    @Before
    public void setUp() {
        mCalculator = new Calculator();
    }

    // Individual test case for addition
    @Test
    public void addTwoNumbers() {
        double resultAdd = mCalculator.add(1.0, 1.0);
        // Assert that resultAdd is equal to 2.0
        assertThat(resultAdd, is(equalTo(2.0)));
    }

    // Test case for division by zero exception handling
    @Test(expected = IllegalArgumentException.class)
    public void divideByZeroThrowsException() {
        mCalculator.divide(15.0, 0.0);
    }
}
```

---

### 3. Configuring Gradle for Test Dependencies

In `build.gradle (Module: app)`:

```groovy
android {
    defaultConfig {
        // Configures AndroidJUnitRunner for instrumented Espresso tests
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
}

dependencies {
    // Local Unit Testing Dependencies (Host JVM)
    testCompile 'junit:junit:4.12'
    testCompile 'org.hamcrest:hamcrest-library:1.3'
    testCompile 'org.mockito:mockito-core:1.10.19'

    // Instrumented Testing Dependencies (On Device / Espresso)
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
}
```

---

## SECTION 3: The Android Support Library (AndroidX)

---

## 1. Purpose & Benefits of the Support Library (Exam Core Question)

The **Android Support Library** (now modernized as **AndroidX**) is a set of static code libraries providing backward-compatible versions of new Android framework APIs, along with specialized UI widgets and utility features.

### Core Benefits:
1. **Backward Compatibility**: Allows apps targeting modern API levels to run newer features (like Material Design, `AppCompatActivity`, `RecyclerView`, `Fragment`) on older Android devices (down to API 9/15).
2. **Convenience & Helper Features**: Provides classes like `ShareCompat.IntentBuilder` and `ContextCompat`.
3. **Multi-Device Adaptability**: Offers layout components (`CoordinatorLayout`, `PercentFrameLayout`) for responsive layouts across phone and tablet screens.

---

## 2. Main Support Library Packages & Features

| Library Package | Min API Level | Key Features & Included Classes |
| :--- | :--- | :--- |
| **`v4 Support Library`** | API 4 / 9 | Large set of backward-compatibility APIs: `Fragment`, `ViewPager`, `NestedScrollView`, `NotificationCompat`, `ShareCompat`. |
| **`v7 Appcompat Library`** | API 7 / 14 | Adds backward-compatible Action Bar / Toolbar support via `AppCompatActivity` and themes (`Theme.AppCompat`). |
| **`v7 RecyclerView`** | API 7 | Modern, high-performance replacement for `ListView`. |
| **`v7 CardView`** | API 7 | UI container widget displaying cards with rounded corners and drop shadows. |
| **`Design Support Library`** | API 7 | Implements Material Design components: `FloatingActionButton`, `NavigationView`, `TabLayout`, `TextInputLayout`, `Snackbar`. |

---

## 3. Adding Support Libraries to `build.gradle`

```groovy
dependencies {
    compile 'com.android.support:appcompat-v7:24.2.1'
    compile 'com.android.support:design:24.2.1'
    compile 'com.android.support:recyclerview-v7:24.2.1'
    compile 'com.android.support:cardview-v7:24.2.1'
}
```

---

## 4. Usage Conventions for Support Library APIs

1. **Fully-Qualified XML Layout Names**: When referencing Support Library View widgets in XML layouts, you **must use their full package names**:
   ```xml
   <android.support.design.widget.CoordinatorLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       android:layout_width="match_parent"
       android:layout_height="match_parent">

       <android.support.design.widget.FloatingActionButton
           android:id="@+id/fab"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:src="@drawable/ic_add" />

   </android.support.design.widget.CoordinatorLayout>
   ```

2. **Checking System Version Programmatically (`Build.VERSION.SDK_INT`)**:
   When using framework APIs that do not have support library equivalents, check the device OS runtime version before invoking higher API methods:

   ```java
   private void setUpActionBar() {
       // Check if device is running Honeycomb (API 11) or higher
       if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
           android.app.ActionBar actionBar = getActionBar();
           if (actionBar != null) {
               actionBar.setDisplayHomeAsUpEnabled(true);
           }
       } else {
           // Fallback logic for older platform releases
       }
   }
   ```
