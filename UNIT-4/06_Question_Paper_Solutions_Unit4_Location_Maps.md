# Question Paper Solutions: Location, Maps, Performance & Publishing (Questions 1a to 5)

---

## Question 1a: Location Permissions in Manifest with Code Segments (5 Marks)

Android offers **two primary location permissions** for apps accessing device location:

1. **`ACCESS_FINE_LOCATION`**:
   - **Description**: Grants precise location access derived from GPS receivers, Wi-Fi hotspots, and cell towers (accuracy within meters).
2. **`ACCESS_COARSE_LOCATION`**:
   - **Description**: Grants approximate location access derived from cell towers and Wi-Fi networks (accuracy within city block / ~1 km).

### Manifest Declarations (`AndroidManifest.xml`):
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.locationapp">

    <!-- Approximate Location Permission -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

    <!-- Precise Location Permission -->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

    <!-- Optional: Mandatory background location for API 29+ -->
    <uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION" />

</manifest>
```

---

## Question 1b: Request & Start Periodic Location Updates Code (5 Marks)

Precise tracking requires `FusedLocationProviderClient`, `LocationRequest`, and `LocationCallback`.

```java
public class MainActivity extends AppCompatActivity {

    private FusedLocationProviderClient mFusedLocationClient;
    private LocationCallback mLocationCallback;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mFusedLocationClient = LocationServices.getFusedLocationProviderClient(this);

        // Define Location Callback to receive continuous updates
        mLocationCallback = new LocationCallback() {
            @Override
            public void onLocationResult(@NonNull LocationResult locationResult) {
                for (Location location : locationResult.getLocations()) {
                    Log.d("LocationUpdate", "Lat: " + location.getLatitude() + 
                            ", Lng: " + location.getLongitude());
                }
            }
        };

        startLocationUpdates();
    }

    private void startLocationUpdates() {
        // Create LocationRequest specifying parameters
        LocationRequest locationRequest = LocationRequest.create()
                .setInterval(10000)        // Desired update interval: 10 seconds
                .setFastestInterval(5000)  // Fastest update interval: 5 seconds
                .setPriority(LocationRequest.PRIORITY_HIGH_ACCURACY); // Uses GPS

        // Runtime permission check before starting updates
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) 
                == PackageManager.PERMISSION_GRANTED) {
            mFusedLocationClient.requestLocationUpdates(locationRequest, mLocationCallback, Looper.getMainLooper());
        }
    }
}
```

---

## Question 2a: Get Last Known Location Code (6 Marks)

The **`FusedLocationProviderClient.getLastLocation()`** method returns the most recent cached device location instantly without waking up GPS hardware, saving battery.

```java
private void getLastKnownLocation() {
    FusedLocationProviderClient client = LocationServices.getFusedLocationProviderClient(this);

    // Verify fine/coarse location permissions
    if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) 
            == PackageManager.PERMISSION_GRANTED) {

        client.getLastLocation().addOnSuccessListener(this, new OnSuccessListener<Location>() {
            @Override
            public void onSuccess(Location location) {
                if (location != null) {
                    double latitude = location.getLatitude();
                    double longitude = location.getLongitude();
                    Log.d("LastLocation", "Latitude: " + latitude + ", Longitude: " + longitude);
                    Toast.makeText(MainActivity.this, 
                            "Location: " + latitude + ", " + longitude, Toast.LENGTH_SHORT).show();
                } else {
                    Log.d("LastLocation", "Location is null (Location services turned off or fresh boot)");
                }
            }
        });
    }
}
```

---

## Question 2b: Geocoding and Reverse Geocoding (`Geocoder` Class) (4 Marks)

### A. The `Geocoder` Class
The **`Geocoder`** class handles transformations between latitude/longitude coordinates and physical street addresses.

```
                            GEOCODING vs REVERSE GEOCODING
                                          │
       Address String ("Bengaluru, India") ──> Geocoding ──> Coordinates (12.9716, 77.5946)
       Coordinates (12.9716, 77.5946) ──> Reverse Geocoding ──> Address String ("Bengaluru")
```

### B. Core Methods:
1. **`getFromLocation(double latitude, double longitude, int maxResults)`** *(Reverse Geocoding)*:
   - Converts coordinates into a list of **`Address`** objects containing street names, city, state, and postal code.
2. **`getFromLocationName(String locationName, int maxResults)`** *(Geocoding)*:
   - Converts a place name string (e.g. "MG Road, Bengaluru") into latitude/longitude coordinates.

```java
// Reverse Geocoding Code Snippet
Geocoder geocoder = new Geocoder(this, Locale.getDefault());
try {
    List<Address> addresses = geocoder.getFromLocation(12.9716, 77.5946, 1);
    if (!addresses.isEmpty()) {
        String city = addresses.get(0).getLocality();
        String country = addresses.get(0).getCountryName();
        Log.d("Geocoder", "City: " + city + ", Country: " + country);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

---

## Question 3: Map Types Offered by Google Maps Android API & Initial Configuration Code (10 Marks)

### A. 5 Map Types Offered by Google Maps Android API (5 Marks)

1. **`MAP_TYPE_NORMAL`**: Standard roadmap view displaying roads, built-in features, and labels.
2. **`MAP_TYPE_SATELLITE`**: Satellite aerial photograph data without road or label overlays.
3. **`MAP_TYPE_HYBRID`**: Satellite aerial photos combined with road network overlays and map labels.
4. **`MAP_TYPE_TERRAIN`**: Topographic data depicting terrain elevation contours, mountain ranges, and rivers.
5. **`MAP_TYPE_NONE`**: Blank grid with no map tiles loaded.

---

### B. Configuring Initial Map State Code (`OnMapReadyCallback`) (5 Marks)

```java
public class MapsActivity extends AppCompatActivity implements OnMapReadyCallback {

    private GoogleMap mMap;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);

        // Obtain SupportMapFragment and notify when map is ready
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        if (mapFragment != null) {
            mapFragment.getMapAsync(this);
        }
    }

    @Override
    public void onMapReady(GoogleMap googleMap) {
        mMap = googleMap;

        // 1. Configure Map Type
        mMap.setMapType(GoogleMap.MAP_TYPE_HYBRID);

        // 2. Enable Zoom Controls UI
        mMap.getUiSettings().setZoomControlsEnabled(true);

        // 3. Set Initial Target Location (Bengaluru)
        LatLng bengaluru = new LatLng(12.9716, 77.5946);

        // 4. Add Marker at initial location
        mMap.addMarker(new MarkerOptions()
                .position(bengaluru)
                .title("Marker in Bengaluru")
                .snippet("Capital of Karnataka"));

        // 5. Configure Camera Position & Zoom Level (Zoom level 15)
        CameraUpdate cameraUpdate = CameraUpdateFactory.newLatLngZoom(bengaluru, 15.0f);
        mMap.moveCamera(cameraUpdate);
    }
}
```

---

## Question 4: Privacy Best Practices for Permissions & App Performance Optimization (10 Marks)

### A. Privacy Best Practices for Permissions (5 Marks)

1. **Minimization**: Only request permissions strictly necessary for core features.
2. **Use System Intents Instead of Raw Permissions**: Use `ACTION_IMAGE_CAPTURE` instead of asking for `CAMERA` permission.
3. **Provide In-Context Rationale**: Explain why a dangerous permission is needed before calling the system permission dialog.
4. **Respect User Denials**: Provide fallback UI gracefully if users deny permissions instead of crashing.
5. **Reset Unused Permissions**: Use Android 11+ auto-reset permission policies for inactive apps.

---

### B. Recommendations for Improving App Performance (5 Marks)

1. **Avoid Main Thread I/O (16ms Render Limit)**: Offload all disk I/O, database queries, and network calls to background threads using `AsyncTaskLoader` or `WorkManager`.
2. **Optimize Layout Hierarchies (Reduce Overdraw)**: Use `ConstraintLayout` to flatten view trees, avoiding deeply nested `LinearLayouts`.
3. **Optimize Memory & Recycler Views**: Use `RecyclerView` instead of `ListView` to recycle item views and prevent Garbage Collection (GC) pauses.
4. **Wireless Radio Data Efficiency**: Batch and prefetch network requests to minimize cellular radio Tail Time power drain.
5. **Code & Resource Shrinking (R8 / ProGuard)**: Enable ProGuard/R8 in `build.gradle` to strip unused code, compress resources, and reduce APK size.

---

## Question 5: High-Level Tasks for Publishing an Android App to Google Play Store (10 Marks)

```
                            APP PUBLISHING HIGH-LEVEL TASKS
                                          │
 1. PREPARATION & POLISHING: Set versionCode & versionName in build.gradle, strip debug logs
                                          │
 2. BUILD SIGNED BUNDLE: Generate Keystore (.jks) and build signed Android App Bundle (.aab)
                                          │
 3. PLAY CONSOLE ACCOUNT: Setup Google Play Developer Account ($25 fee)
                                          │
 4. STORE LISTING ASSETS: Provide Title, Description, App Icon (512x512), Screenshots, Privacy Policy
                                          │
 5. COMPLIANCE & REVIEW: Complete Content Rating Questionnaire & submit AAB to Production Track for review
```

### High-Level Publishing Steps:

1. **App Preparation & Configuration**:
   - Strip out debug logging statements (`Log.d`).
   - Configure `versionCode` (integer) and `versionName` (string) in `app/build.gradle`.
   - Test release build variant for performance and layout bugs.
2. **Generate Signed Release Bundle**:
   - In Android Studio, navigate to **Build > Generate Signed Bundle / APK**.
   - Create a secure **Keystore (`.jks`)** file specifying store password, alias, and key password.
   - Build a signed **Android App Bundle (`.aab`)**.
3. **Google Play Console Registration**:
   - Register a Google Play Developer Account ($25 one-time fee).
4. **Store Listing & Branding**:
   - Set app title, short description, and full description.
   - Upload branding graphics: High-res Icon (512x512 PNG), Feature Graphic (1024x500), and phone/tablet screenshots.
5. **Content Rating & Privacy Policies**:
   - Complete the international IARC Content Rating questionnaire.
   - Provide a valid, publicly hosted **Privacy Policy URL**.
6. **Upload & Production Release**:
   - Create a production release track in Play Console and upload the `.aab` file.
   - Submit the app for Google's automated security scanning and manual policy review.
