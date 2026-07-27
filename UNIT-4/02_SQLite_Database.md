# Storing Data Using SQLite Database

---

## 1. Overview of SQLite in Android

**SQLite** is an open-source, serverless, self-contained, zero-configuration relational database engine embedded directly into the Android OS.

```
                            SQLITE DATABASE ARCHITECTURE
                                         |
     +-----------------------------------+-----------------------------------+
     |                                   |                                   |
     v                                   v                                   v
SQLITEOPENHELPER                SQLITEDATABASE                      CURSOR
(Manages DB creation &          (Executes CRUD queries:             (Pointers iterating over
 schema upgrades)                insert, query, update, delete)      query result rows)
```

### Key SQLite Characteristics:
- **Private Access**: By default, SQLite database files are stored in `/data/data/<package_name>/databases/` and are private to the application.
- **Dynamic Typing**: SQLite uses dynamic typing for values (`NULL`, `INTEGER`, `REAL`, `TEXT`, `BLOB`), unlike standard SQL servers which enforce static column types.

---

## 2. Key Architecture Classes: `SQLiteOpenHelper`, `SQLiteDatabase`, `Cursor` (Exam 10-Mark Core Question)

### A. `SQLiteOpenHelper`
An abstract helper class used to manage database creation, versioning, and schema upgrades.

#### Mandatory Override Callbacks:
1. **`onCreate(SQLiteDatabase db)`**: Called automatically when the database is created for the **very first time**. Used to execute `CREATE TABLE` DDL statements.
2. **`onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)`**: Called automatically when the database version number (`DATABASE_VERSION`) increases. Used to execute `DROP TABLE IF EXISTS` or `ALTER TABLE` schema migration statements.

---

### B. `SQLiteDatabase`
Represents the SQLite database instance and provides methods for executing CRUD operations (`insert()`, `query()`, `update()`, `delete()`, `execSQL()`, `rawQuery()`).

---

### C. `Cursor` & `ContentValues`

- **`ContentValues`**: A key-value dictionary object used to store data pairs for `insert()` and `update()` operations (e.g. `contentValues.put("title", "Android Dev")`).
- **`Cursor`**: A pointer interface offering read-write access to the result set returned by a database query (`query()` or `rawQuery()`).

#### Key `Cursor` Navigation Methods:
- `moveToFirst()`, `moveToNext()`, `moveToPosition(int)`
- `getColumnIndexOrThrow(String columnName)`
- `getString(int columnIndex)`, `getInt(int columnIndex)`
- `close()` (Mandatory to close to prevent memory/cursor leaks!)

---

## 3. CRUD Operations Matrix

| CRUD Operation | `SQLiteDatabase` Method | Parameters Passed | Return Value |
| :--- | :--- | :--- | :--- |
| **Create (Insert)** | `insert(table, nullColumnHack, values)` | Table name, `null`, `ContentValues` object. | `long` row ID of newly inserted row (-1 if error). |
| **Read (Query)** | `query(table, columns, selection, selectionArgs, groupBy, having, orderBy)` | Table name, Projection array, `WHERE` clause, `WHERE` arguments. | `Cursor` object pointing to result rows. |
| **Update** | `update(table, values, whereClause, whereArgs)` | Table name, `ContentValues`, `WHERE` clause, `WHERE` arguments. | `int` number of affected database rows. |
| **Delete** | `delete(table, whereClause, whereArgs)` | Table name, `WHERE` clause, `WHERE` arguments. | `int` number of deleted database rows. |

---

## 4. Complete Executable Code Pattern: SQLite Implementation

### A. Contract Class (`WordListContract.java`)
Defines schema constants, table names, and column names centrally:

```java
public final class WordListContract {
    private WordListContract() {} // Prevent instantiation

    public static class WordEntry implements BaseColumns {
        public static final String TABLE_NAME = "word_list";
        public static final String COLUMN_WORD = "word";
    }
}
```

---

### B. OpenHelper & Database Manager (`WordListOpenHelper.java`)

```java
public class WordListOpenHelper extends SQLiteOpenHelper {

    private static final String DATABASE_NAME = "wordlist.db";
    private static final int DATABASE_VERSION = 1;

    // SQL statement to create table
    private static final String SQL_CREATE_WORDLIST_TABLE =
            "CREATE TABLE " + WordListContract.WordEntry.TABLE_NAME + " (" +
            WordListContract.WordEntry._ID + " INTEGER PRIMARY KEY AUTOINCREMENT, " +
            WordListContract.WordEntry.COLUMN_WORD + " TEXT NOT NULL);";

    public WordListOpenHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    // 1. Called on FIRST creation
    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL_CREATE_WORDLIST_TABLE);
    }

    // 2. Called on DB version upgrade
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS " + WordListContract.WordEntry.TABLE_NAME);
        onCreate(db);
    }

    // --- CRUD HELPER METHODS ---

    // CREATE (Insert)
    public long insertWord(String word) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put(WordListContract.WordEntry.COLUMN_WORD, word);

        return db.insert(WordListContract.WordEntry.TABLE_NAME, null, values);
    }

    // READ (Query All)
    public Cursor getAllWords() {
        SQLiteDatabase db = this.getReadableDatabase();
        String[] projection = {
                WordListContract.WordEntry._ID,
                WordListContract.WordEntry.COLUMN_WORD
        };

        return db.query(
                WordListContract.WordEntry.TABLE_NAME,
                projection,
                null, null, null, null, null
        );
    }

    // UPDATE
    public int updateWord(int id, String newWord) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put(WordListContract.WordEntry.COLUMN_WORD, newWord);

        String selection = WordListContract.WordEntry._ID + " = ?";
        String[] selectionArgs = { String.valueOf(id) };

        return db.update(WordListContract.WordEntry.TABLE_NAME, values, selection, selectionArgs);
    }

    // DELETE
    public int deleteWord(int id) {
        SQLiteDatabase db = this.getWritableDatabase();
        String selection = WordListContract.WordEntry._ID + " = ?";
        String[] selectionArgs = { String.valueOf(id) };

        return db.delete(WordListContract.WordEntry.TABLE_NAME, selection, selectionArgs);
    }
}
```

---

### C. Reading from `Cursor` in Activity (`MainActivity.java`)

```java
WordListOpenHelper mDB = new WordListOpenHelper(this);

// Query all records
Cursor cursor = mDB.getAllWords();

if (cursor != null && cursor.moveToFirst()) {
    do {
        int idIndex = cursor.getColumnIndexOrThrow(WordListContract.WordEntry._ID);
        int wordIndex = cursor.getColumnIndexOrThrow(WordListContract.WordEntry.COLUMN_WORD);

        int id = cursor.getInt(idIndex);
        String word = cursor.getString(wordIndex);

        Log.d("SQLiteData", "ID: " + id + ", Word: " + word);
    } while (cursor.moveToNext());

    // Mandatory: Always close cursor to avoid leaks!
    cursor.close();
}
```
