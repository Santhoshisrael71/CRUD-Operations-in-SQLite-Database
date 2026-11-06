# CRUD-Operations-in-SQLite-Database


#  Country Currency Management App

An Android application developed using **Java and SQLite Database** to store, view, add, update, and delete country names along with their respective currencies.

This project demonstrates CRUD (Create, Read, Update, Delete) operations using SQLite Database and displays records using Android ListView.

##  Project Overview

The Country Currency Management App allows users to maintain a list of countries and their currencies in a local SQLite database.

Users can:
- View all country and currency records.
- Add new country records.
- Modify existing country details.
- Update country names and currencies.
- Delete unwanted records.

##  Objectives

- To develop an Android application using Java.
- To understand SQLite database connectivity.
- To implement CRUD operations.
- To display database records using ListView.
- To understand Android Activity navigation.

##  Technologies Used

| Technology | Purpose |
|---|---|
| Java | Application development |
| Android Studio | Development environment |
| SQLite | Local database |
| XML | User interface design |
| ListView | Display country records |
| Gradle | Project build management |

##  Project Structure

```text
CountryCurrencySQLite/
│
├── app/
│   ├── src/
│   │   └── main/
│   │       ├── java/
│   │       │   └── com/example/countrycurrency/
│   │       │       ├── DatabaseHelper.java
│   │       │       ├── DBManager.java
│   │       │       ├── CountryListActivity.java
│   │       │       ├── AddCountryActivity.java
│   │       │       └── ModifyCountryActivity.java
│   │       │
│   │       ├── res/
│   │       │   └── layout/
│   │       │       ├── activity_country_list.xml
│   │       │       ├── activity_add_country.xml
│   │       │       └── activity_modify_country.xml
│   │       │
│   │       └── AndroidManifest.xml
│   │
│   └── build.gradle
│
├── outputs/
│   ├── 01_list_view_output.png
│   ├── 02_add_country_output.png
│   └── 03_modify_country_output.png
│
├── build.gradle
├── settings.gradle
├── gradle.properties
└── README.md
```

##  Database Design

**Database Name:** CountryDB.db

**Table Name:** countries

| Column Name | Data Type | Description |
|---|---|---|
| _id | INTEGER | Primary key |
| country | TEXT | Country name |
| currency | TEXT | Currency name |

### SQL Table Creation

```sql
CREATE TABLE countries (
    _id INTEGER PRIMARY KEY AUTOINCREMENT,
    country TEXT NOT NULL,
    currency TEXT NOT NULL
);
```

##  Application Modules

### 1. DatabaseHelper.java

- Creates the SQLite database.
- Creates the countries table.
- Manages database version.
- Handles database upgrades.

### 2. DBManager.java

Handles all database operations.

- `insert()` – Adds a new record.
- `getAllCountries()` – Retrieves all records.
- `getCountry()` – Retrieves a selected record.
- `update()` – Updates an existing record.
- `delete()` – Deletes a selected record.

### 3. CountryListActivity.java

- Displays country names and currencies.
- Uses ListView to display records.
- Provides an Add Country button.
- Allows selecting and deleting records.

### 4. AddCountryActivity.java

- Accepts country name.
- Accepts currency name.
- Inserts records into SQLite database.
- Displays success or failure messages.

### 5. ModifyCountryActivity.java

- Loads selected country details.
- Allows editing country information.
- Updates records in SQLite.
- Deletes selected records.

##  CRUD Operations

| Operation | Description |
|---|---|
| Create | Add a new country and currency |
| Read | Display all records in ListView |
| Update | Modify existing country details |
| Delete | Remove a country record |

##  Application Outputs

### 1. Country ListView Screen

Displays all saved countries and their currencies.

![Country ListView](outputs/01_list_view_output.png)

### 2. Add Country Screen

Allows users to enter a country name and currency.

![Add Country](outputs/02_add_country_output.png)

### 3. Modify Country Screen

Allows users to update or delete an existing record.

![Modify Country](outputs/03_modify_country_output.png)

*Note: The images above are illustrative UI mockups of the expected application screens.*

##  Sample Database Records

| Country | Currency |
|---|---|
| India | Indian Rupee (INR) |
| United States | US Dollar (USD) |
| Japan | Japanese Yen (JPY) |
| United Kingdom | Pound Sterling (GBP) |
| Canada | Canadian Dollar (CAD) |
| Australia | Australian Dollar (AUD) |

##  How to Run the Project

1. Download or clone this repository.
2. Open Android Studio.
3. Select **Open Project**.
4. Choose the CountryCurrencySQLite folder.
5. Wait for Gradle Sync to complete.
6. Start an Android Emulator or connect an Android device.
7. Click the Run button.
8. The Country Currency application will launch.

##  How to Use the Application

**Adding a country:**
1. Click Add Country.
2. Enter the country name.
3. Enter the currency name.
4. Click Save Country.

**Updating a country:**
1. Select a country from ListView.
2. Modify the country or currency details.
3. Click Update Record.

**Deleting a country:**
1. Long-press a country in ListView, or open its edit screen.
2. Confirm the Delete action.
3. The record will be removed from SQLite.

##  Learning Outcomes

- Understanding Android application development.
- Working with SQLiteOpenHelper.
- Creating and managing SQLite tables.
- Implementing CRUD operations.
- Using Cursor to retrieve database records.
- Displaying database records using ListView.
- Handling multiple Android Activities.
- Passing data between Activities using Intent.

##  Future Enhancements

- Search countries by name.
- Add country flags.
- Prevent duplicate country records.
- Add sorting and filtering options.
- Improve UI using RecyclerView.
- Add currency symbols and exchange rates.

##  Author

**Immanuel Santhosh P**

Android Development | Java | SQLite Database

##  License

This project is created for educational and academic purposes.

