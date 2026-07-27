# Advanced Android Programming: Internet, Entertainment & Services

---

## SECTION 1: Advanced Internet Connectivity & Web Services

Modern Android applications rely heavily on web services (RESTful APIs), asynchronous network handling, and WebViews to present dynamic cloud content.

```
                    ADVANCED INTERNET CONNECTIVITY ARCHITECTURE
                                         |
     +-----------------------------------+-----------------------------------+
     |                                   |                                   |
     v                                   v                                   v
BACKGROUND THREADING                REST API / JSON                   WEBVIEW COMPONENT
(AsyncTask / Executor /            (HttpURLConnection /              (Embedding HTML5 web
 Network Security Config)           JSON Parsing)                     pages inside Native App)
```

---

### 1. Network Security Configuration (`res/xml/network_security_config.xml`)

Starting in Android 9.0 (API 28), cleartext HTTP (`http://`) traffic is disabled by default for security. Apps must use HTTPS or explicitly define a network security configuration.

```xml
<!-- res/xml/network_security_config.xml -->
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">api.example.com</domain>
    </domain-config>
</network-security-config>
```

---

### 2. Embedded Web View (`WebView`)

A **`WebView`** allows you to display web content (HTML/JS) directly within an Activity layout.

#### XML Layout Component:
```xml
<WebView
    android:id="@+id/webview"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

#### Java Implementation & Security Controls:
```java
WebView webView = findViewById(R.id.webview);

// Enable JavaScript execution (Use with caution)
WebSettings webSettings = webView.getSettings();
webSettings.setJavaScriptEnabled(true);

// Ensure links open inside the app's WebView rather than external browser
webView.setWebViewClient(new WebViewClient());

// Load remote URL or local assets
webView.loadUrl("https://developer.android.com");
```

---

## SECTION 2: Entertainment: Audio, Video & Media Playback

Android provides media APIs for playing audio/video files from local raw resources, device storage, or network streams.

---

### 1. `MediaPlayer` Architecture (Audio Playback)

The **`MediaPlayer`** class manages playback of audio and video files.

```
                         MEDIAPLAYER STATE MACHINE
                                     |
                  Idle ──── setDataSource() ────> Initialized
                                                       │
                                                    prepareAsync()
                                                       │
                                                       v
                   Paused <─── pause() ─── Playing <── Prepared / start()
```

#### Executable Audio Playback Code Pattern:
```java
// A. Play local raw resource (res/raw/sample_audio.mp3)
MediaPlayer mediaPlayer = MediaPlayer.create(this, R.raw.sample_audio);
mediaPlayer.start(); // Start playback

// Release resources when finished (Mandatory to avoid audio memory leak)
mediaPlayer.setOnCompletionListener(mp -> {
    mp.release();
});

// B. Play streaming audio from URL asynchronously
MediaPlayer streamPlayer = new MediaPlayer();
try {
    streamPlayer.setDataSource("https://www.example.com/audio.mp3");
    streamPlayer.prepareAsync(); // Non-blocking prepare on background thread
    
    streamPlayer.setOnPreparedListener(mp -> {
        mp.start(); // Start playing once buffer is ready
    });
} catch (IOException e) {
    e.printStackTrace();
}
```

---

### 2. Video Playback using `VideoView` & `MediaController`

A **`VideoView`** encapsulates video playback and surface management, paired with **`MediaController`** for UI playback controls (Play, Pause, Seek bar).

```java
VideoView videoView = findViewById(R.id.videoview);
String videoUrl = "https://www.example.com/sample_video.mp4";

// Attach MediaController (Play/Pause UI overlay)
MediaController mediaController = new MediaController(this);
mediaController.setAnchorView(videoView);
videoView.setMediaController(mediaController);

// Set video source and start playback
videoView.setVideoURI(Uri.parse(videoUrl));
videoView.requestFocus();
videoView.start();
```

---

## SECTION 3: Advanced Background Services & Integration

Connecting services with system frameworks enables rich background entertainment and location capabilities.

---

### 1. Foreground Entertainment Services (Audio Streaming Service)

Background audio playback MUST run as a **Foreground Service** to prevent the Android OS from killing playback when the app moves to the background.

```java
public class AudioPlaybackService extends Service {

    private MediaPlayer mMediaPlayer;
    private static final int NOTIFICATION_ID = 201;

    @Override
    public void onCreate() {
        super.onCreate();
        mMediaPlayer = MediaPlayer.create(this, R.raw.sample_audio);
        mMediaPlayer.setLooping(true);
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        // 1. Create Notification Channel (API 26+)
        createNotificationChannel();

        // 2. Build Ongoing Media Notification
        Notification notification = new NotificationCompat.Builder(this, "MEDIA_CHANNEL")
                .setContentTitle("Playing Music")
                .setContentText("Background Audio Stream")
                .setSmallIcon(R.drawable.ic_music_note)
                .setOngoing(true)
                .build();

        // 3. Promote Service to Foreground State
        startForeground(NOTIFICATION_ID, notification);

        // Start playback
        mMediaPlayer.start();
        return START_STICKY;
    }

    @Override
    public void onDestroy() {
        if (mMediaPlayer != null) {
            mMediaPlayer.stop();
            mMediaPlayer.release();
        }
        super.onDestroy();
    }

    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    private void createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationChannel channel = new NotificationChannel(
                    "MEDIA_CHANNEL", "Media Service Channel",
                    NotificationManager.IMPORTANCE_LOW);
            NotificationManager manager = getSystemService(NotificationManager.class);
            if (manager != null) manager.createNotificationChannel(channel);
        }
    }
}
```

---

### 2. Location-Based Services (`FusedLocationProviderClient`)

Entertainment and internet services often require user location (e.g. mapping, nearby events).

#### Manifest Permissions Required:
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

#### Obtaining Device Location:
```java
FusedLocationProviderClient locationClient = 
    LocationServices.getFusedLocationProviderClient(this);

// Check permission before querying location
if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) 
        == PackageManager.PERMISSION_GRANTED) {
    
    locationClient.getLastLocation().addOnSuccessListener(this, location -> {
        if (location != null) {
            double latitude = location.getLatitude();
            double longitude = location.getLongitude();
            Log.d("LocationService", "Lat: " + latitude + ", Long: " + longitude);
        }
    });
}
```
