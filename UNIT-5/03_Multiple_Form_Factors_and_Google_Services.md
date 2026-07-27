# Multiple Form Factors & Using Google Services

---

## SECTION 1: Multiple Form Factors in Android

Android powers a diverse ecosystem of devices with varying screen sizes, resolutions, aspect ratios, and hardware form factors.

```
                           ANDROID FORM FACTORS ECOSYSTEM
                                         |
     +-----------------------------------+-----------------------------------+
     |                                   |                                   |
     v                                   v                                   v
MOBILE PHONES & TABLETS              WEAR OS & TV                     AUTO & FOLDABLES
(Resource qualifiers:                (Custom UI surfaces &             (Dynamic screen resizing
 sw600dp, w720dp, land)               leanback layouts)                & multi-window support)
```

---

### 1. Supporting Multiple Screen Sizes & Orientations (Exam 10-Mark Core Question)

Android handles different form factors through **Resource Qualifiers** in directory names.

#### A. Configuration Resource Qualifiers:
- **Smallest Width (`sw<N>dp`) Qualifier**: Specifies the minimum screen width regardless of current orientation.
  - `res/layout/` $\rightarrow$ Standard phones ($< 600\text{dp}$).
  - `res/layout-sw600dp/` $\rightarrow$ 7-inch tablets ($\ge 600\text{dp}$).
  - `res/layout-sw720dp/` $\rightarrow$ 10-inch tablets ($\ge 720\text{dp}$).
- **Orientation Qualifier**:
  - `res/layout/` $\rightarrow$ Portrait mode (default).
  - `res/layout-land/` $\rightarrow$ Landscape mode.

---

### 2. Multi-Pane Layout Pattern (Fragments for Tablets)

Single-pane UI on phones combines into a **Master-Detail (Multi-Pane)** layout on tablets using **Fragments**.

```
    HANDHELD PHONE LAYOUT                    TABLET MULTI-PANE LAYOUT
    (res/layout/activity_main.xml)           (res/layout-sw600dp/activity_main.xml)
     +-------------------------+              +-------------------+-------------------+
     |    Master Item List     |              | Master Item List  | Detail Item View  |
     |    (Fragment A)         |              | (Fragment A)      | (Fragment B)      |
     +-------------------------+              +-------------------+-------------------+
```

#### Code Implementation (`res/layout-sw600dp/activity_main.xml`):
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <!-- Fragment A: Master List -->
    <fragment
        android:id="@+id/fragment_list"
        android:name="com.example.MasterListFragment"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />

    <!-- Fragment B: Detail View (Side-by-Side on Tablets) -->
    <fragment
        android:id="@+id/fragment_detail"
        android:name="com.example.DetailFragment"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="2" />

</LinearLayout>
```

---

### 3. Alternative Screen Densities (DPI Assets)

Android categorizes screens into Density Bucket qualifiers to serve crisp bitmap images without blur:

| Density Bucket Qualifier | Screen Density Ratio | DPI Range | Asset Directory |
| :--- | :--- | :--- | :--- |
| **mdpi** (Baseline) | $1.0\times$ | ~160 dpi | `drawable-mdpi/` |
| **hdpi** | $1.5\times$ | ~240 dpi | `drawable-hdpi/` |
| **xhdpi** | $2.0\times$ | ~320 dpi | `drawable-xhdpi/` |
| **xxhdpi** | $3.0\times$ | ~480 dpi | `drawable-xxhdpi/` |
| **xxxhdpi** | $4.0\times$ | ~640 dpi | `drawable-xxxhdpi/` |

> [!TIP]
> **Best Practice**: Use **Vector Drawables (`<vector>`)** instead of PNG bitmaps wherever possible. Vectors scale losslessly across all screen densities with zero density overhead!

---

## SECTION 2: Using Google Services (Google Play Services)

**Google Play Services** is a client library and background service running on Android devices that provides access to Google APIs (Maps, Location, Sign-In, Cloud Messaging).

---

### 1. Google Play Services Architecture

```
                       GOOGLE PLAY SERVICES ARCHITECTURE
                                       |
    CLIENT APP                                          DEVICE SYSTEM
+----------------+      GoogleApi / Client Client     +-----------------------+
|  Your Android  | ---------------------------------> |  Google Play Services |
|  Application   |                                    |    APK (Background)   |
+----------------+ <--------------------------------- +-----------+-----------+
                          Returns Data / Callbacks                |
                                                                  v
                                                      +-----------------------+
                                                      |   Google Cloud APIs   |
                                                      +-----------------------+
```

---

### 2. Checking Google Play Services Availability

Before using Google APIs, apps MUST verify that Google Play Services APK is installed and up-to-date on the target device:

```java
public boolean checkGooglePlayServices(Activity activity) {
    GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
    int resultCode = apiAvailability.isGooglePlayServicesAvailable(activity);

    if (resultCode != ConnectionResult.SUCCESS) {
        if (apiAvailability.isUserResolvableError(resultCode)) {
            // Displays a system dialog prompting user to update/install Play Services
            apiAvailability.getErrorDialog(activity, resultCode, 9000).show();
        } else {
            Toast.makeText(activity, "Device not supported.", Toast.LENGTH_LONG).show();
        }
        return false;
    }
    return true; // Play Services is available!
}
```

---

### 3. Google Sign-In Integration (`GoogleSignInClient`)

Allows users to authenticate securely using their existing Google Account credentials.

#### Step 1: Add Dependency (`build.gradle`)
```groovy
implementation 'com.google.android.gms:play-services-auth:20.6.0'
```

#### Step 2: Implementation Workflow (`LoginActivity.java`)
```java
public class LoginActivity extends AppCompatActivity {

    private GoogleSignInClient mGoogleSignInClient;
    private static final int RC_SIGN_IN = 9001;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        // 1. Configure Google Sign-In options
        GoogleSignInOptions gso = new GoogleSignInOptions.Builder(GoogleSignInOptions.DEFAULT_SIGN_IN)
                .requestEmail()
                .build();

        mGoogleSignInClient = GoogleSignIn.getClient(this, gso);

        // 2. Set click listener on Sign-In button
        findViewById(R.id.sign_in_button).setOnClickListener(v -> signIn());
    }

    private void signIn() {
        Intent signInIntent = mGoogleSignInClient.getSignInIntent();
        startActivityForResult(signInIntent, RC_SIGN_IN);
    }

    // 3. Handle Sign-In result
    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if (requestCode == RC_SIGN_IN) {
            Task<GoogleSignInAccount> task = GoogleSignIn.getSignedInAccountFromIntent(data);
            try {
                GoogleSignInAccount account = task.getResult(ApiException.class);
                // Signed in successfully! Access account details:
                String name = account.getDisplayName();
                String email = account.getEmail();
                Log.d("GoogleSignIn", "Welcome " + name + " (" + email + ")");
            } catch (ApiException e) {
                Log.w("GoogleSignIn", "Sign-in failed code=" + e.getStatusCode());
            }
        }
    }
}
```
