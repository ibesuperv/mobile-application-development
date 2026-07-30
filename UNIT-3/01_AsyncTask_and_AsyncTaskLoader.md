# Background Processing: AsyncTask and AsyncTaskLoader

---

## 1. The Android Threading Model & ANR (Exam Core Question)

### A. The Main Thread (UI Thread)

In Android, the system creates a single thread of execution for the application called the **Main Thread (UI Thread)**.

- **Role of UI Thread**: Dispatches user interaction events to the appropriate UI widgets and renders visual frames (drawing the layout).
- **The Single Thread Model Rules (Exam 4-Mark Rule)**:
  1. **Do NOT block the UI thread**: Never perform long-running tasks (network requests, database queries, bitmap decoding, file I/O) on the main thread.
  2. **Do NOT access the Android UI toolkit from outside the UI thread**: Worker/background threads cannot directly update UI components (e.g. `textView.setText()` from a background thread will throw a `CalledFromWrongThreadException`).

---

### B. Application Not Responding (ANR) Error

```
+-------------------------------------------------------------------+
|               MAIN THREAD (UI THREAD) BLOCKED                     |
|                                                                   |
|   User Input Event        Network / I/O Task (Blocks > 5 sec)     |
|   [ Click / Swipe ]  ---> [========== STALLS UI THREAD ==========]|
|                                                                   |
|   ===> OS Triggers ANR Dialog ("App is Not Responding")           |
+-------------------------------------------------------------------+
```

- **Definition**: If an app blocks the main UI thread for more than **5 seconds** without responding to user input events (or broadcast receivers taking > 10 seconds), the OS displays an **ANR (Application Not Responding)** dialog, giving the user the option to force-close the app.

---

## 2. `AsyncTask` Architecture & Lifecycle

`AsyncTask` is an abstract helper class provided by Android to perform short background operations (a few seconds) and publish results back to the Main Thread without manually manipulating raw `Thread` or `Handler` objects.

### A. Generic Types Signature: `AsyncTask<Params, Progress, Result>`

```java
public class MyAsyncTask extends AsyncTask<Params, Progress, Result> { ... }
```

1. **`Params`**: Type of parameters sent to the task upon execution (`execute(Params...)`). Passed into `doInBackground()`.
2. **`Progress`**: Type of progress units published during background computation. Passed into `onProgressUpdate()`.
3. **`Result`**: Type of result returned by the background calculation. Passed into `onPostExecute()`.
4. _Note_: To omit any parameter type, use the `Void` class type (e.g., `AsyncTask<Void, Void, String>`).

---

### B. The 4 Callback Methods & Thread Execution Matrix (Exam 10-Mark Core Question)

```
        MAIN THREAD                     BACKGROUND THREAD
             |                                  |
   onPreExecute()                               |  (1. Runs before task)
             |                                  |
             +--------------------------------->|  doInBackground(Params...)
             |                                  |  (2. Long-running computation)
             |<---------------------------------+  (Calls publishProgress())
   onProgressUpdate(Progress...)                |  (3. Updates UI progress bar)
             |                                  |
             |<---------------------------------+  (Task completes, returns Result)
   onPostExecute(Result)                        |  (4. Receives result & updates UI)
```

| Method Name                         | Executing Thread      | Purpose & Description                                                                                                                                      | Can Access UI Views? |
| :---------------------------------- | :-------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------: |
| **`onPreExecute()`**                | **Main (UI) Thread**  | Invoked automatically before the background computation starts. Used to setup UI (e.g., show a `ProgressBar`).                                             |       **YES**        |
| **`doInBackground(Params...)`**     | **Background Thread** | Performs the actual heavy calculation or network task. Receives `Params...` and returns `Result`. Invokes `publishProgress()` to trigger progress updates. |        **NO**        |
| **`onProgressUpdate(Progress...)`** | **Main (UI) Thread**  | Triggered on the main thread after `publishProgress()` is called inside `doInBackground()`. Used to update live UI progress.                               |       **YES**        |
| **`onPostExecute(Result)`**         | **Main (UI) Thread**  | Executed on the main thread after `doInBackground()` finishes and returns a result. Receives the `Result` object to display final UI updates.              |       **YES**        |

---

### C. Complete Executable Code Pattern: `AsyncTask`

```java
public class MyTask extends AsyncTask<Void, Integer, String> {

    @Override
    protected void onPreExecute() {
        System.out.println("Task Started");
    }

    @Override
    protected String doInBackground(Void... params) {

        for(int i = 0; i <= 100; i += 20) {

            publishProgress(i);

            try {
                Thread.sleep(1000);
            } catch (Exception e) { }

        }

        return "Download Complete";
    }

    @Override
    protected void onProgressUpdate(Integer... values) {

        System.out.println("Progress : " + values[0] + "%");

    }

    @Override
    protected void onPostExecute(String result) {

        System.out.println(result);

    }
}

```

---

## 3. Limitations & Flaws of `AsyncTask` (Exam 6-Mark Question)

While simple, `AsyncTask` suffers from severe structural flaws:

1. **Configuration Changes (Screen Rotation)**:
   - When the screen rotates, the current `Activity` instance is destroyed and recreated.
   - However, a running `AsyncTask` remains alive attached to the **old destroyed Activity instance**.
   - **Result**: Updating UI elements in `onPostExecute()` will target stale/destroyed views, causing crashes or silently failing.
2. **Memory Leaks**:
   - Non-static inner `AsyncTask` classes hold an implicit reference to the enclosing `Activity`. If the Activity is destroyed while `doInBackground()` is running, the garbage collector cannot reclaim the Activity memory, causing a severe memory leak.
3. **Execution Limits**:
   - Tasks executed with `.execute()` are queued on a single background thread (`SERIAL_EXECUTOR`). Long-running tasks stall subsequent tasks.

---

## 4. `AsyncTaskLoader` & Loader Architecture

`AsyncTaskLoader` was introduced in Android 3.0 (API 11) to solve the configuration change and memory leak problems of `AsyncTask`.

```
                         LOADERMANAGER ARCHITECTURE
                                     |
    +--------------------------------+--------------------------------+
    |                                                                 |
    v                                                                 v
ACTIVITY / FRAGMENT                                         LOADERMANAGER
(Subscribes to callbacks)                                   (Persists across rotation)
    |                                                                 |
    +-------------------> LoaderManager.LoaderCallbacks <-------------+
                                     |
                                     v
                             AsyncTaskLoader<D>
                     (Runs loadInBackground() off UI thread)
```

---

### A. Key Advantages of `AsyncTaskLoader` over `AsyncTask`:

1. **Survives Configuration Changes**: Loaders are retained by the `LoaderManager` across Activity destruction and recreation caused by screen rotation. They do not re-fetch data unnecessarily.
2. **Prevents Memory Leaks**: Automatically disconnects from destroyed activities and destroys the loader when the hosting Activity is permanently finished.
3. **Event Monitoring**: Can continuously monitor underlying data sources and auto-deliver fresh content when underlying data updates.

---

### B. The 3 `LoaderManager.LoaderCallbacks<D>` Callback Methods

To use an `AsyncTaskLoader`, an Activity implements `LoaderManager.LoaderCallbacks<D>`:

1. **`onCreateLoader(int id, Bundle args)`**:
   - Instantiates and returns a new `AsyncTaskLoader` for the given ID.
2. **`onLoadFinished(Loader<D> loader, D data)`**:
   - Called on the **UI thread** when the loader has finished loading data (`loadInBackground()` completes). Updates the UI with `data`.
3. **`onLoaderReset(Loader<D> loader)`**:
   - Called when a previously created loader is being reset, making its data unavailable. Clears references to stale data.

---

### C. Complete Executable Code Pattern: `AsyncTaskLoader`

#### 1. Custom Loader Class (`FetchBookLoader.java`):

```java
public class FetchBookLoader extends AsyncTaskLoader<String> {

    private String mQueryString;

    public FetchBookLoader(Context context, String queryString) {
        super(context);
        this.mQueryString = queryString;
    }

    // Mandatory: Must override onStartLoading() to trigger forceLoad()
    @Override
    protected void onStartLoading() {
        super.onStartLoading();
        forceLoad(); // Triggers loadInBackground() execution
    }

    // Heavy background execution (Replaces doInBackground)
    @Override
    public String loadInBackground() {
        return NetworkUtils.getBookInfo(mQueryString);
    }
}
```

#### 2. Activity Implementation (`MainActivity.java`):

```java
public class MainActivity extends AppCompatActivity
        implements LoaderManager.LoaderCallbacks<String> {

    private static final int BOOK_LOADER_ID = 101;
    private TextView mResultText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mResultText = (TextView) findViewById(R.id.result_text);

        // Re-connect to existing loader or init new one
        if (getSupportLoaderManager().getLoader(BOOK_LOADER_ID) != null) {
            getSupportLoaderManager().initLoader(BOOK_LOADER_ID, null, this);
        }
    }

    public void searchBooks(View view) {
        Bundle queryBundle = new Bundle();
        queryBundle.putString("queryString", "Java Programming");
        // Start new search query loader
        getSupportLoaderManager().restartLoader(BOOK_LOADER_ID, queryBundle, this);
    }

    // 1. Create Loader instance
    @Override
    public Loader<String> onCreateLoader(int id, Bundle args) {
        String queryString = "";
        if (args != null) {
            queryString = args.getString("queryString");
        }
        return new FetchBookLoader(this, queryString);
    }

    // 2. Receive background result on UI thread
    @Override
    public void onLoadFinished(Loader<String> loader, String data) {
        mResultText.setText(data); // Safely update UI
    }

    // 3. Clean up stale references
    @Override
    public void onLoaderReset(Loader<String> loader) {
        mResultText.setText("");
    }
}
```
