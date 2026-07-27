# Web Pages, Maps, Communication (SMS & Email) & Sensors

---

## SECTION 1: Displaying Web Pages & Google Maps

Integrating web pages and mapping services into Android applications allows apps to render web content and display geographical points of interest.

---

### 1. Displaying Web Pages (`CustomTabsIntent` vs `WebView`)

| Approach | Rendering Engine | Security & Performance | Best Use Case |
| :--- | :--- | :--- | :--- |
| **`WebView`** | Embedded WebKit / Chromium layout inside Activity. | High UI integration; requires custom JS security management. | Displaying local HTML content, web forms, or custom hybrid UI. |
| **`CustomTabsIntent` (Chrome Custom Tabs)** | System browser engine sharing cookies and saved passwords. | **Fast & Secure**: Shares user's browser state and saved credentials. | Opening external links, OAuth logins, privacy policies. |

```java
// Displaying external Web Page using Chrome Custom Tabs
String url = "https://developer.android.com";
CustomTabsIntent.Builder builder = new CustomTabsIntent.Builder();
CustomTabsIntent customTabsIntent = builder.build();
customTabsIntent.launchUrl(this, Uri.parse(url));
```

---

### 2. Google Maps API (`SupportMapFragment` & `GoogleMap`)

Google Maps integration relies on `MapView` or `SupportMapFragment`.

#### Step 1: Manifest API Key & Permissions
```xml
<manifest ...>
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <application ...>
        <!-- Google Maps API Key -->
        <meta-data
            android:name="com.google.android.geo.API_KEY"
            android:value="YOUR_GOOGLE_MAPS_API_KEY" />
    </application>
</manifest>
```

#### Step 2: Implementation in Activity (`OnMapReadyCallback`)
```java
public class MapsActivity extends AppCompatActivity implements OnMapReadyCallback {

    private GoogleMap mMap;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);

        // Obtain SupportMapFragment and request map asynchronously
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        if (mapFragment != null) {
            mapFragment.getMapAsync(this);
        }
    }

    @Override
    public void onMapReady(GoogleMap googleMap) {
        mMap = googleMap;

        // Add Marker at Lat/Long coordinates
        LatLng newYork = new LatLng(40.7128, -74.0060);
        mMap.addMarker(new MarkerOptions().position(newYork).title("Marker in New York"));
        
        // Move Camera & Set Zoom Level
        mMap.moveCamera(CameraUpdateFactory.newLatLngZoom(newYork, 15.0f));
    }
}
```

---

## SECTION 2: Communicating with SMS & Emails

Android apps can initiate SMS messages and emails either directly via **System Manager Services** or indirectly via **Implicit Intents**.

---

### 1. Sending SMS Messages

#### Approach A: Implicit Intent (No Permissions Required)
Launches the default system SMS app with pre-filled text:

```java
Intent smsIntent = new Intent(Intent.ACTION_SENDTO);
smsIntent.setData(Uri.parse("smsto:+1234567890")); // Target phone number
smsIntent.putExtra("sms_body", "Hello! This is a test SMS.");
startActivity(smsIntent);
```

#### Approach B: Direct SMS (`SmsManager`)
Sends SMS directly from within the application (Requires `SEND_SMS` permission):

```xml
<uses-permission android:name="android.permission.SEND_SMS" />
```

```java
public void sendDirectSMS(String phoneNumber, String message) {
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.SEND_SMS) 
            == PackageManager.PERMISSION_GRANTED) {
        
        SmsManager smsManager = SmsManager.getDefault();
        smsManager.sendTextMessage(phoneNumber, null, message, null, null);
        Toast.makeText(this, "SMS Sent Successfully", Toast.LENGTH_SHORT).show();
    }
}
```

---

### 2. Sending Emails via Implicit Intent

```java
Intent emailIntent = new Intent(Intent.ACTION_SENDTO);
emailIntent.setData(Uri.parse("mailto:")); // Only email apps handle this
emailIntent.putExtra(Intent.EXTRA_EMAIL, new String[] { "recipient@example.com" });
emailIntent.putExtra(Intent.EXTRA_SUBJECT, "Exam Study Notes Inquiry");
emailIntent.putExtra(Intent.EXTRA_TEXT, "Body text of the email message.");

if (emailIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(emailIntent);
}
```

---

## SECTION 3: Sensors Framework

Android provides a sensor framework to access raw data from built-in hardware sensors (accelerometer, gyroscope, light sensor, proximity sensor).

---

### 1. The Sensor Framework Trio (Exam Core 10-Mark Question)

```
                            SENSOR FRAMEWORK TRIO
                                      |
     +--------------------------------+--------------------------------+
     |                                |                                |
     v                                v                                v
SENSORMANAGER                       SENSOR                           SENSOREVENT
(System service accessing           (Class representing a            (Data object containing
 sensor hardware)                    specific hardware sensor)        raw sensor readings)
```

1. **`SensorManager`**: System service instance (`getSystemService(SENSOR_SERVICE)`). Manages listeners and queries available sensors.
2. **`Sensor`**: Represents physical sensor hardware constants (`Sensor.TYPE_ACCELEROMETER`, `Sensor.TYPE_LIGHT`, `Sensor.TYPE_GYROSCOPE`, `Sensor.TYPE_PROXIMITY`).
3. **`SensorEvent`**: Holds raw sensor measurement values (`values[]` float array), accuracy level, and timestamp.
4. **`SensorEventListener`**: Interface receiving real-time sensor updates (`onSensorChanged()`, `onAccuracyChanged()`).

---

### 2. Complete Executable Code Pattern: Sensor Monitoring

```java
public class SensorActivity extends AppCompatActivity implements SensorEventListener {

    private SensorManager mSensorManager;
    private Sensor mAccelerometer;
    private TextView mSensorTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_sensor);

        mSensorTextView = findViewById(R.id.sensor_data_textview);

        // 1. Get SensorManager instance
        mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);

        // 2. Get default Accelerometer sensor
        if (mSensorManager != null) {
            mAccelerometer = mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
        }
    }

    // 3. Register listener in onResume() to conserve battery
    @Override
    protected void onResume() {
        super.onResume();
        if (mAccelerometer != null) {
            mSensorManager.registerListener(
                this, 
                mAccelerometer, 
                SensorManager.SENSOR_DELAY_NORMAL
            );
        }
    }

    // 4. Unregister listener in onPause() to prevent battery drain (CRITICAL EXAM RULE)
    @Override
    protected void onPause() {
        super.onPause();
        if (mSensorManager != null) {
            mSensorManager.unregisterListener(this);
        }
    }

    // 5. Mandatory Callback: Called whenever new sensor data arrives
    @Override
    public void onSensorChanged(SensorEvent event) {
        if (event.sensor.getType() == Sensor.TYPE_ACCELEROMETER) {
            float x = event.values[0]; // Acceleration force along X axis
            float y = event.values[1]; // Acceleration force along Y axis
            float z = event.values[2]; // Acceleration force along Z axis

            mSensorTextView.setText(String.format("X: %.2f | Y: %.2f | Z: %.2f", x, y, z));
        }
    }

    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {
        // Handle changes in sensor accuracy if needed
    }
}
```
