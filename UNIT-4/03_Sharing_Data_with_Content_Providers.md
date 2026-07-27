# Sharing Data with Content Providers

---

## 1. Overview: Content Provider & Content Resolver Architecture

A **`ContentProvider`** is an Android core application component that manages access to a central repository of data, allowing applications to securely share data with other applications across process boundaries.

```
                          CONTENT PROVIDER ARCHITECTURE
                                       |
    CLIENT APP                                              PROVIDER APP
+------------------+     Query / Insert / Update     +------------------------+
|   UI Activity    | ------------------------------> |    ContentProvider     |
|        │         |                                 | (overrides 6 callbacks)|
|        v         |                                 +-----------+------------+
| ContentResolver  | <------------------------------             |
+------------------+         Returns Cursor                      v
                                                     +------------------------+
                                                     | Repository / SQLite DB |
                                                     +------------------------+
```

---

### The Roles of `ContentResolver` vs. `ContentProvider` (Exam 4-Mark Distinction):

- **`ContentProvider` (Server Side)**: Implemented by the app owning the data to expose a standard, secure CRUD API (`query`, `insert`, `update`, `delete`, `getType`, `onCreate`).
- **`ContentResolver` (Client Side)**: Used by external client applications to query or mutate data. Acts as the client proxy that communicates with the `ContentProvider`.

---

## 2. Structure of a Content URI

Applications send requests to a Content Provider using **Uniform Resource Identifiers (URIs)**.

$$\text{content://authority/path/id}$$

```
   scheme       authority                       path              id
  content://  com.example.wordprovider.provider / words          / 5
  └───────┘  └─────────────────────────────────┘ └────┘         └──┘
   Standard    Unique domain registered in        Target data   Target row
   Prefix      AndroidManifest.xml                table/type    ID
```

1. **Scheme**: Always `content://` for content URIs.
2. **Authority**: Unique string identifying the provider (customarily `package_name.provider`).
3. **Path**: Identifies the specific data table or resource path (e.g. `words`).
4. **ID** (Optional): Specifies a single target row index (e.g. `/5`).

---

## 3. MIME Types in Content Providers (`getType()`)

`getType(Uri uri)` returns the **MIME type** of the data pointed to by a Content URI:

- **Multiple Records (Directory)**: `vnd.android.cursor.dir/vnd.<authority>.<path>`
- **Single Record (Item)**: `vnd.android.cursor.item/vnd.<authority>.<path>`

```java
@Override
public String getType(Uri uri) {
    switch (sUriMatcher.match(uri)) {
        case ALL_WORDS:
            return "vnd.android.cursor.dir/vnd.com.example.wordprovider.words";
        case SINGLE_WORD:
            return "vnd.android.cursor.item/vnd.com.example.wordprovider.words";
        default:
            return null;
    }
}
```

---

## 4. The 6 Mandatory Callback Methods of `ContentProvider` (Exam 10-Mark Question)

When creating a custom `ContentProvider`, you MUST extend `ContentProvider` and override 6 methods:

1. **`onCreate()`**: Initializes the provider and underlying database helper. Returns `true` if initialized successfully.
2. **`query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)`**: Returns a `Cursor` containing requested data rows.
3. **`insert(Uri uri, ContentValues values)`**: Inserts a new record and returns the `Uri` of the newly created row.
4. **`update(Uri uri, ContentValues values, String selection, String[] selectionArgs)`**: Updates matching rows and returns the count of updated rows (`int`).
5. **`delete(Uri uri, String selection, String[] selectionArgs)`**: Deletes matching rows and returns the count of deleted rows (`int`).
6. **`getType(Uri uri)`**: Returns the MIME type of the given Content URI.

---

## 5. Security & Manifest Declaration

To expose a Content Provider to other apps, declare it inside `<application>` in `AndroidManifest.xml`:

```xml
<manifest ...>
    <!-- 1. Client app must request custom permission -->
    <uses-permission android:name="com.example.wordprovider.READ_DATA" />

    <application ...>
        <!-- 2. Provider App Manifest Declaration -->
        <provider
            android:name=".WordListContentProvider"
            android:authorities="com.example.wordprovider.provider"
            android:exported="true"
            android:readPermission="com.example.wordprovider.READ_DATA"
            android:writePermission="com.example.wordprovider.WRITE_DATA" />
    </application>
</manifest>
```

- **`android:exported="true"`**: Makes the provider accessible to other apps on the device.
- **`readPermission` / `writePermission`**: Restricts unauthorized external access.

---

## 6. Complete Executable Code Pattern: ContentProvider & UriMatcher

### A. The Provider Implementation (`WordListContentProvider.java`)

```java
public class WordListContentProvider extends ContentProvider {

    public static final String AUTHORITY = "com.example.wordprovider.provider";
    public static final Uri CONTENT_URI = Uri.parse("content://" + AUTHORITY + "/words");

    private static final int ALL_WORDS = 10;
    private static final int SINGLE_WORD = 20;

    private static final UriMatcher sUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);

    static {
        // Match content://com.example.wordprovider.provider/words
        sUriMatcher.addURI(AUTHORITY, "words", ALL_WORDS);
        // Match content://com.example.wordprovider.provider/words/# (# represents integer ID)
        sUriMatcher.addURI(AUTHORITY, "words/#", SINGLE_WORD);
    }

    private WordListOpenHelper mDB;

    @Override
    public boolean onCreate() {
        mDB = new WordListOpenHelper(getContext());
        return true;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection,
                        String[] selectionArgs, String sortOrder) {
        SQLiteDatabase db = mDB.getReadableDatabase();
        Cursor cursor;

        switch (sUriMatcher.match(uri)) {
            case ALL_WORDS:
                cursor = db.query("word_list", projection, selection, selectionArgs, null, null, sortOrder);
                break;
            case SINGLE_WORD:
                selection = "_id = ?";
                selectionArgs = new String[]{ uri.getLastPathSegment() };
                cursor = db.query("word_list", projection, selection, selectionArgs, null, null, sortOrder);
                break;
            default:
                throw new IllegalArgumentException("Unknown URI: " + uri);
        }

        // Notify resolver of data changes
        cursor.setNotificationUri(getContext().getContentResolver(), uri);
        return cursor;
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        SQLiteDatabase db = mDB.getWritableDatabase();
        long id = db.insert("word_list", null, values);
        getContext().getContentResolver().notifyChange(uri, null);
        return Uri.parse(CONTENT_URI + "/" + id);
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs) {
        SQLiteDatabase db = mDB.getWritableDatabase();
        int rowsUpdated = db.update("word_list", values, selection, selectionArgs);
        getContext().getContentResolver().notifyChange(uri, null);
        return rowsUpdated;
    }

    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        SQLiteDatabase db = mDB.getWritableDatabase();
        int rowsDeleted = db.delete("word_list", selection, selectionArgs);
        getContext().getContentResolver().notifyChange(uri, null);
        return rowsDeleted;
    }

    @Override
    public String getType(Uri uri) {
        switch (sUriMatcher.match(uri)) {
            case ALL_WORDS:
                return "vnd.android.cursor.dir/vnd.com.example.wordprovider.words";
            case SINGLE_WORD:
                return "vnd.android.cursor.item/vnd.com.example.wordprovider.words";
            default:
                return null;
        }
    }
}
```

---

### B. Client App Querying via `ContentResolver` (`MainActivity.java`)

```java
Uri wordsUri = Uri.parse("content://com.example.wordprovider.provider/words");

// Query ContentProvider via ContentResolver
Cursor cursor = getContentResolver().query(
        wordsUri,
        null, // All columns
        null, // All rows
        null,
        null  // Default sort order
);

if (cursor != null && cursor.moveToFirst()) {
    do {
        String word = cursor.getString(cursor.getColumnIndexOrThrow("word"));
        Log.d("ClientQuery", "Retrieved Word: " + word);
    } while (cursor.moveToNext());

    cursor.close();
}
```
