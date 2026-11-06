# CRUD Operations in SQLite Database


```
Author or Name : Santhosh P
```
An Android application developed using **Java, SQLite Database and ListView** to store and manage country names and their respective currencies.

The application performs all four CRUD operations:

- Create – Add a new country.
- Read – Display country records.
- Update – Modify existing records.
- Delete – Remove country records.

---

##  Project Information

| Details | Description |
|---|---|
| Project Name | Country Currency Management |
| Platform | Android |
| Programming Language | Java |
| Database | SQLite |
| User Interface | XML |
| Display Component | ListView |
| IDE | Android Studio |

##  Technologies Used

- Java
- Android Studio
- SQLite Database
- XML Layout
- SQLiteOpenHelper
- SQLiteDatabase
- Cursor
- ListView

---

#  Project Structure

```text
CountryCurrencySQLite/
│
├── app/
│   ├── src/main/
│   │   ├── java/com/example/countrycurrency/
│   │   │   ├── DatabaseHelper.java
│   │   │   ├── DBManager.java
│   │   │   ├── CountryListActivity.java
│   │   │   ├── AddCountryActivity.java
│   │   │   └── ModifyCountryActivity.java
│   │   │
│   │   ├── res/layout/
│   │   │   ├── activity_country_list.xml
│   │   │   ├── activity_add_country.xml
│   │   │   └── activity_modify_country.xml
│   │   │
│   │   └── AndroidManifest.xml
│   │
│   └── build.gradle
│
├── outputs/
│   ├── 01_list_view_output.png
│   ├── 02_add_country_output.png
│   └── 03_modify_country_output.png
│
├── settings.gradle
├── build.gradle
├── gradle.properties
└── README.md
```

---

#  COMPLETE SOURCE CODE

Package name: `com.example.countrycurrency`

## 1. DatabaseHelper.java

Creates the SQLite database and countries table.

```java
package com.example.countrycurrency;

import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

public class DatabaseHelper extends SQLiteOpenHelper {

    public static final String DATABASE_NAME = "CountryDB.db";
    public static final int DATABASE_VERSION = 1;

    public static final String TABLE_NAME = "countries";
    public static final String COL_ID = "_id";
    public static final String COL_COUNTRY = "country";
    public static final String COL_CURRENCY = "currency";

    public DatabaseHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        String sql = "CREATE TABLE " + TABLE_NAME + " (" +
                COL_ID + " INTEGER PRIMARY KEY AUTOINCREMENT, " +
                COL_COUNTRY + " TEXT NOT NULL, " +
                COL_CURRENCY + " TEXT NOT NULL)";

        db.execSQL(sql);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db,
                          int oldVersion, int newVersion) {
        // Add database migration code here.
    }
}
```

## 2. DBManager.java

Performs Create, Read, Update and Delete operations.

```java
package com.example.countrycurrency;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;

public class DBManager {

    private final DatabaseHelper helper;
    private SQLiteDatabase db;

    public DBManager(Context context) {
        helper = new DatabaseHelper(context);
    }

    public void open() {
        db = helper.getWritableDatabase();
    }

    public void close() {
        if (db != null && db.isOpen()) {
            db.close();
        }
    }

    // CREATE
    public long insert(String country, String currency) {
        ContentValues values = new ContentValues();

        values.put(DatabaseHelper.COL_COUNTRY, country);
        values.put(DatabaseHelper.COL_CURRENCY, currency);

        return db.insert(DatabaseHelper.TABLE_NAME,
                null, values);
    }

    // READ ALL
    public Cursor getAllCountries() {
        return db.query(DatabaseHelper.TABLE_NAME,
                null, null, null, null, null,
                DatabaseHelper.COL_COUNTRY + " ASC");
    }

    // READ ONE
    public Cursor getCountry(long id) {
        return db.query(DatabaseHelper.TABLE_NAME,
                null,
                DatabaseHelper.COL_ID + "=?",
                new String[]{String.valueOf(id)},
                null, null, null);
    }

    // UPDATE
    public int update(long id, String country,
                      String currency) {

        ContentValues values = new ContentValues();

        values.put(DatabaseHelper.COL_COUNTRY, country);
        values.put(DatabaseHelper.COL_CURRENCY, currency);

        return db.update(DatabaseHelper.TABLE_NAME,
                values,
                DatabaseHelper.COL_ID + "=?",
                new String[]{String.valueOf(id)});
    }

    // DELETE
    public int delete(long id) {
        return db.delete(DatabaseHelper.TABLE_NAME,
                DatabaseHelper.COL_ID + "=?",
                new String[]{String.valueOf(id)});
    }
}
```

## 3. CountryListActivity.java

Displays all country records using ListView.

```java
package com.example.countrycurrency;

import android.app.Activity;
import android.app.AlertDialog;
import android.content.Intent;
import android.database.Cursor;
import android.os.Bundle;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;

import java.util.ArrayList;

public class CountryListActivity extends Activity {

    DBManager manager;
    ListView listView;
    TextView emptyText;
    ArrayList<Long> ids = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_country_list);

        listView = findViewById(R.id.listView);
        emptyText = findViewById(R.id.emptyText);

        Button addButton = findViewById(R.id.addButton);

        manager = new DBManager(this);
        manager.open();

        addButton.setOnClickListener(v -> {
            startActivity(new Intent(this,
                    AddCountryActivity.class));
        });

        listView.setOnItemClickListener((parent, view,
                                         position, id) -> {

            Intent intent = new Intent(this,
                    ModifyCountryActivity.class);

            intent.putExtra("country_id",
                    ids.get(position));

            startActivity(intent);
        });

        listView.setOnItemLongClickListener(
                (parent, view, position, id) -> {

            long selectedId = ids.get(position);

            new AlertDialog.Builder(this)
                    .setTitle("Delete Country")
                    .setMessage("Delete this country?")
                    .setPositiveButton("Delete",
                            (dialog, which) -> {
                        manager.delete(selectedId);
                        loadCountries();
                    })
                    .setNegativeButton("Cancel", null)
                    .show();

            return true;
        });
    }

    private void loadCountries() {

        ArrayList<String> items = new ArrayList<>();
        ids.clear();

        try (Cursor cursor = manager.getAllCountries()) {

            while (cursor.moveToNext()) {

                long id = cursor.getLong(
                        cursor.getColumnIndexOrThrow(
                                DatabaseHelper.COL_ID));

                String country = cursor.getString(
                        cursor.getColumnIndexOrThrow(
                                DatabaseHelper.COL_COUNTRY));

                String currency = cursor.getString(
                        cursor.getColumnIndexOrThrow(
                                DatabaseHelper.COL_CURRENCY));

                ids.add(id);
                items.add(country + " - " + currency);
            }
        }

        ArrayAdapter<String> adapter =
                new ArrayAdapter<>(this,
                        android.R.layout.simple_list_item_1,
                        items);

        listView.setAdapter(adapter);

        emptyText.setText(items.isEmpty()
                ? "No countries added yet." : "");

        emptyText.setVisibility(items.isEmpty()
                ? TextView.VISIBLE : TextView.GONE);
    }

    @Override
    protected void onResume() {
        super.onResume();

        if (manager != null) {
            loadCountries();
        }
    }

    @Override
    protected void onDestroy() {
        if (manager != null) {
            manager.close();
        }
        super.onDestroy();
    }
}
```

## 4. AddCountryActivity.java

Adds a new country and currency to SQLite.

```java
package com.example.countrycurrency;

import android.app.Activity;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class AddCountryActivity extends Activity {

    DBManager manager;
    EditText countryInput, currencyInput;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_country);

        countryInput = findViewById(R.id.countryInput);
        currencyInput = findViewById(R.id.currencyInput);

        Button saveButton = findViewById(R.id.saveButton);

        manager = new DBManager(this);
        manager.open();

        saveButton.setOnClickListener(v -> {

            String country = countryInput.getText()
                    .toString().trim();

            String currency = currencyInput.getText()
                    .toString().trim();

            if (country.isEmpty() || currency.isEmpty()) {

                Toast.makeText(this,
                        "Enter both fields",
                        Toast.LENGTH_SHORT).show();
                return;
            }

            long result = manager.insert(country, currency);

            if (result != -1) {

                Toast.makeText(this,
                        "Country Added Successfully",
                        Toast.LENGTH_SHORT).show();

                finish();

            } else {

                Toast.makeText(this,
                        "Failed to Add Country",
                        Toast.LENGTH_SHORT).show();
            }
        });
    }

    @Override
    protected void onDestroy() {
        if (manager != null) {
            manager.close();
        }
        super.onDestroy();
    }
}
```

## 5. ModifyCountryActivity.java

Updates and deletes existing country records.

```java
package com.example.countrycurrency;

import android.app.Activity;
import android.app.AlertDialog;
import android.database.Cursor;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class ModifyCountryActivity extends Activity {

    DBManager manager;
    EditText countryInput, currencyInput;
    long countryId;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_modify_country);

        countryInput = findViewById(R.id.countryInput);
        currencyInput = findViewById(R.id.currencyInput);

        Button updateButton = findViewById(R.id.updateButton);
        Button deleteButton = findViewById(R.id.deleteButton);

        countryId = getIntent().getLongExtra(
                "country_id", -1L);

        manager = new DBManager(this);
        manager.open();

        if (countryId == -1L) {
            finish();
            return;
        }

        try (Cursor cursor = manager.getCountry(countryId)) {

            if (cursor.moveToFirst()) {

                countryInput.setText(cursor.getString(
                        cursor.getColumnIndexOrThrow(
                                DatabaseHelper.COL_COUNTRY)));

                currencyInput.setText(cursor.getString(
                        cursor.getColumnIndexOrThrow(
                                DatabaseHelper.COL_CURRENCY)));
            } else {
                Toast.makeText(this, "Record not found",
                        Toast.LENGTH_SHORT).show();
                finish();
                return;
            }
        }

        // UPDATE
        updateButton.setOnClickListener(v -> {

            String country = countryInput.getText()
                    .toString().trim();

            String currency = currencyInput.getText()
                    .toString().trim();

            if (country.isEmpty() || currency.isEmpty()) {
                Toast.makeText(this,
                        "Enter both fields",
                        Toast.LENGTH_SHORT).show();
                return;
            }

            int result = manager.update(
                    countryId, country, currency);

            if (result > 0) {
                Toast.makeText(this,
                        "Record Updated",
                        Toast.LENGTH_SHORT).show();
                finish();
            }
        });

        // DELETE
        deleteButton.setOnClickListener(v -> {

            new AlertDialog.Builder(this)
                    .setTitle("Delete Country")
                    .setMessage("Are you sure?")
                    .setPositiveButton("Delete",
                            (dialog, which) -> {

                        manager.delete(countryId);

                        Toast.makeText(this,
                                "Record Deleted",
                                Toast.LENGTH_SHORT).show();

                        finish();
                    })
                    .setNegativeButton("Cancel", null)
                    .show();
        });
    }

    @Override
    protected void onDestroy() {
        if (manager != null) {
            manager.close();
        }
        super.onDestroy();
    }
}
```

---

#  XML LAYOUT FILES

## 6. activity_country_list.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <Button
        android:id="@+id/addButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Add Country"/>

    <TextView
        android:id="@+id/emptyText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"/>

    <ListView
        android:id="@+id/listView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</LinearLayout>
```

## 7. activity_add_country.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="20dp">

    <EditText
        android:id="@+id/countryInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter Country Name"/>

    <EditText
        android:id="@+id/currencyInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter Currency"/>

    <Button
        android:id="@+id/saveButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Save Country"/>

</LinearLayout>
```

## 8. activity_modify_country.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="20dp">

    <EditText
        android:id="@+id/countryInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Country Name"/>

    <EditText
        android:id="@+id/currencyInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Currency"/>

    <Button
        android:id="@+id/updateButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Update Record"/>

    <Button
        android:id="@+id/deleteButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Delete Record"/>

</LinearLayout>
```

---

# 📄 AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:allowBackup="true"
        android:label="Country Currency"
        android:theme="@android:style/Theme.Material.Light.NoActionBar">

        <activity
            android:name=".ModifyCountryActivity"
            android:exported="false"/>

        <activity
            android:name=".AddCountryActivity"
            android:exported="false"/>

        <activity
            android:name=".CountryListActivity"
            android:exported="true">

            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>

        </activity>
    </application>
</manifest>
```

---

# 🗄️ DATABASE DESIGN

**Database Name:** CountryDB.db

**Table Name:** countries

| Column | Data Type | Description |
|---|---|---|
| _id | INTEGER | Primary Key |
| country | TEXT | Country Name |
| currency | TEXT | Currency Name |

### SQL Query

```sql
CREATE TABLE countries (
    _id INTEGER PRIMARY KEY AUTOINCREMENT,
    country TEXT NOT NULL,
    currency TEXT NOT NULL
);
```

---

#  CRUD OPERATIONS

| CRUD | Java Method | Function |
|---|---|---|
| CREATE | insert() | Insert country |
| READ | getAllCountries() | Display countries |
| UPDATE | update() | Modify country |
| DELETE | delete() | Remove country |

---



---

#  SAMPLE RECORDS

| Country | Currency |
|---|---|
| India | Indian Rupee (INR) |
| United States | US Dollar (USD) |
| Japan | Japanese Yen (JPY) |
| United Kingdom | Pound Sterling (GBP) |
| Canada | Canadian Dollar (CAD) |
| Australia | Australian Dollar (AUD) |

---

#  HOW TO RUN

1. Download or clone the repository.
2. Open Android Studio.
3. Select Open Project.
4. Choose the CountryCurrencySQLite folder.
5. Wait for Gradle Sync.
6. Start an Android Emulator.
7. Click Run.
8. The application will open.

---

#  LEARNING OUTCOMES

- Android application development using Java.
- SQLite database creation and management.
- CRUD operation implementation.
- ListView data display.
- Cursor-based database retrieval.
- Activity navigation using Intent.
- XML layout design.

---

#  FUTURE ENHANCEMENTS

- Search country by name.
- Add country flags.
- Prevent duplicate entries.
- Add currency symbols.
- Improve the interface using RecyclerView.

---

#  AUTHOR

**Immanuel Santhosh P**

Technology: Android | Java | SQLite

---

# OUTPUT

<img width="720" height="1280" alt="01_list_view_output" src="https://github.com/user-attachments/assets/a1d53e18-c5f5-4e4c-a1b7-b50c8be57454" />

<img width="720" height="1280" alt="02_add_country_output" src="https://github.com/user-attachments/assets/aeb77054-2781-4982-99b9-4e3e07b02f88" />

<img width="720" height="1280" alt="03_modify_country_output" src="https://github.com/user-attachments/assets/0ec9e21e-69d6-43a7-9e36-71d36b0a6bf5" />


#  RESULT

Successfully Run Android application developed using **Java, SQLite Database and ListView** to store and manage country names and their respective currencies.
