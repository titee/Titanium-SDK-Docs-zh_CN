# Working with a SQLite Database

Creating and Installing Databases

Creating a database
Installing a database
Manipulating DB Contents

Storing data
Retrieving data
Updating data
Deleting data
Truncating (emptying) a table
Manipulating the Database's Structure

Dropping a table
Altering a table
PRAGMA commands
Best Practices

Close the database and resultset with each operation
Use transactions to speed batch inserts
Use a minimal pre-populated database
Store a version number to ease database updates
Hands-on Practice
References and Further Reading
Objective

In this chapter, you will learn how to interact with the built-in SQLite3 RDMS with the Ti.Database module. You'll learn how to create databases and tables. You'll retrieve data. And you'll store data in the database.
Contents

SQLite3 is version 3 of SQLite's SQL-based relational database management system (RDMS), chosen by Apple, Google and RIM to provide local data storage on their mobile devices. SQLite is currently the world's most widely-used database. It owes it's popularity to its open source code, low resource footprint and, as it does not rely on any installed services unlike almost all other DBMSs, its simplicity of setup and maintenance.

There are a few things to note when you first work with SQLite, that may influence the way you develop with it:

SQLite's data is stored in a simple text file. There is no granular security or user privileges for data. Anyone with filesystem access to it may read its contents.
There are only five underlying data types; TEXT, NUMERIC, INTEGER, REAL, NONE. Datatypes In SQLite explains them in more detail.
Binary objects (BLOBs) are stored as text representations, thus access to BLOBs is not optimal. We recommend you store BLOBs on the filesystem and store the filesystem path in the database.
SQLite supports concurrent read access, but enforces sequential write access. This is because a filesystem lock is placed on the file during write operations. This is an important point to bear in mind with multi-threaded applications.
Referential integrity is not enabled by default. See SQLite Foreign Key Support for more information.
RIGHT and FULL OUTER JOINs are not supported.
There is limited ALTER TABLE support; columns may not be modified or deleted.
Titanium provides access to SQLite via its Titanium.Database.ResultSet object, which holds the data returned from those queries.

Creating and Installing Databases
You have a couple of options for creating or installing the database that your application will use:

Create an empty database and define its structure and contents via SQL statements embedded within your app.
Install a predefined database (with or without data) that is shipped with your app.
Creating a database
To create a database, you'll use the Titanium.Database.open() method – if the database you specify to open doesn't exist, Titanium will create it for you. Whether the database exists or Titanium creates it for you, this call will return a database connection handle that you'll use in subsequent database operations.

var db = Ti.Database.open('weatherDB');
There are a few platform specifics:


On iOS, the database file that's created is automatically assigned the .sql extension, while on Android no extension is added.
On iOS 5, database files are stored in the app's Private Documents folder (on device); on iOS 4, it is stored in the Application Support/database folder.
On iOS 5.0.1+, the database will be included in any other user data backed up to iCloud. See below for more info.
On Android, the database is created on the internal storage (you could move it, or use the install procedure to put it on external storage). The standard location on internal storage is /data/data/com.example.yourappid/databases/dbname
Once you have created a database, your next step would be to get your structure set up. Titanium does not include abstraction libraries; you'll interact with the database using standard SQL statements. You can use the CREATE TABLE IF NOT EXISTS statement to safely create tables – if they already exist, the statement will be ignored. (Don't know SQL? There are lots of tutorials available, such as this one, this one, or this Google search resultset.)

//bootstrap the database
var db = Ti.Database.open('TiBountyHunter');
db.execute('CREATE TABLE IF NOT EXISTS fugitives(id INTEGER PRIMARY KEY, name TEXT, captured INTEGER, url TEXT, capturedLat REAL, capturedLong REAL);');
db.close();
You might notice that we closed the database when done. Closing database connections and resultsets is important to do for performance reasons. You're dealing with a single-user, memory constrained environment in which connection pooling is not important. The time to open/close connections is less of a hit that consuming memory by keeping connections open.

Controlling backups to iCloud
Apps may be rejected by Apple for backing up unnecessary or inappropriate files. To avoid this rejection, you can mark files and databases so that they're not backed up.

var db = Ti.Database.open('TiBountyHunter');
db.file.setRemoteBackup(false);
This value should only need to be set once by your app, but setting it multiple times will not cause problems. For files distributed with your app, this will need to be set on app startup. This flag will only affect iOS versions 5.0.1 and later, but is safe to set on earlier versions. Note that setting this property to false will also prevent the file identified by this object from being backed up to iTunes.

For more information, see:

Database file property
The setRemoteBackup() method
Apple Technical Q&A 1719 describes the types of data that should or should not be backed up
Installing a database
Your other option is to install a pre-existing database (we'll cover how to create a SQLite database later in this guide). For that, you'll use the install() method copy a pre-existing database file from Titanium's Resources directory, or one of its descendants, to the appropriate directory on the device/simulator. As with open(), this method returns a reference to the opened database. If a file already exists with the same name, the copy action will silently fail and the database will simply be opened (and reference returned).

var db = Ti.Database.install('/mydata/weatherDB', 'weatherDB');
In this example, weatherDB, initially stored in the Resources/mydata/ directory, will be copied to the application's databases directory (see notes above on where that is). We recommend you spell out the absolute path to the database file, with / corresponding to your app's Resources directory. If you do not, the source file might not be found. On Android, the system will create an empty database with the given name. But since it contains no tables, your app will crash on your first attempt at access the database.

Creating a SQLite database
You'll need some sort of tool to create your SQLite database. There are many to choose from, both commercial and free. One of the most popular is the SQLite Manager plugin for Firefox. The SQLite project maintains a rather large list of management tools on their site, too.
Manipulating DB Contents
SQLite (and by extension Titanium) supports a fairly complete list of SQL statements. You'll use these to manipulate the data in your database. Typical tasks include inserting new data, retrieving data, updating data, and deleting data.

Storing data
In order to insert records to a database, you should pass an SQL INSERT statement to the execute() method. While you could use string concatenation to construct the INSERT statement, SQLite and Titanium's execute() method support the safer technique of using unnamed parameterized queries. In other words, you enter question marks as placemarkers where you would like each variable containing your data to be substituted, as follows:

db.execute('INSERT INTO city (name,continent,temp_f,temp_c,condition_id) VALUES (?,?,?,?,?)', importName, importContinent, importTempF, importTempC, dbConditionId);
It's commonplace for tables to include an auto-incrementing primary key, representing a unique ID for each row. You would typically use such IDs to access individual rows to update them or delete them. You can obtain the row ID associated with a newly inserted row by examining the lastInsertRowID property, like this:

db.execute('INSERT INTO city (name,continent,temp_f,temp_c,condition_id) VALUES (?,?,?,?,?)', importName, importContinent, importTempF, importTempC, dbConditionId);
var lastID = db.lastInsertRowID; // presumes `city` has an auto-increment column
Retrieving data
You can retrieve data from the database using a typical SQL SELECT query, passed to execute() like the examples above. This method returns a Titanium.Database.ResultSet object, which you use to access the queried data.

(The execute() method always returns a result set object, but it's not all that useful with CREATE TABLE and other queries shown previously.)

The result set data is available using the methods that the Titanium.Database.ResultSet object provides. In the following code, a loop is used to iterate through each row, and only ends when isValidRow() returns false. The fieldByName() method allows you to refer to each field by its database column name, or an alias you assign in the SQL statement. At each iteration of the loop, it is necessary to move the ResultSet row pointer to the next row, using the next() method.

var cityWeatherRS = db.execute('SELECT id,name,continent FROM city');
while (cityWeatherRS.isValidRow())
{
var cityId = cityWeatherRS.fieldByName('id');
var cityName = cityWeatherRS.fieldByName('name');
var cityContinent = cityWeatherRS.fieldByName('continent');
Ti.API.info(cityId + ' ' + cityName + ' ' + cityContinent);
cityWeatherRS.next();
}
cityWeatherRS.close();
You may join related tables together, just as you would in standard SQL. The following returns all the city records and includes the weather conditions summary for each, if it exists.

db.execute('SELECT city.id,name,cond.id,cond.summary,cond.icon FROM city LEFT JOIN condition cond WHERE city.condition_id = cond.id');
Of course, you can select subsets of data by providing conditions within your SELECT statements. For example, the following returns only those records where the weather summary is "Partly Cloudy":

var cityWeatherRS = db.execute('SELECT city.id AS city_id,name,cond.id AS cond_id,cond.summary,cond.icon FROM city LEFT JOIN condition cond WHERE city.condition_id = cond.id WHERE cond.summary=?', "Partly Cloudy");
Notice here that the city.id and cond.id columns have been aliased. This is because the fieldByName() method would not be able to distinguish between them if you simply passed their column name, fieldByName(id). Thus, the ResultSet code would look like this:

while (cityWeatherRS.isValidRow())
{
var cityId = cityWeatherRS.fieldByName('city_id');
var cityName = cityWeatherRS.fieldByName('name');
var cityConditionId = cityWeatherRS.fieldByName('cond_id');
var cityConditionSummary = cityWeatherRS.fieldByName('summary');
var cityConditionIcon = cityWeatherRS.fieldByName('icon');
Ti.API.info(cityId + ' ' + cityName + ' ' + cityConditionId + ' ' + cityConditionSummary + ' ' + cityConditionIcon);
cityWeatherRS.next();
}
cityWeatherRS.close();
Updating data
To update a database row you would use the UPDATE statement along with parameterized queries as shown above for inserting data. For example:

db.execute('UPDATE condition SET icon=? WHERE id=?',importIcon,dbConditionId);
Deleting data
You can delete one or more rows from a table using the DELETE statement. Make sure to supply a condition or all rows in the table will be deleted. SQLite also supports the DROP TABLE syntax to remove an entire table from the database.

db.execute('DELETE FROM city WHERE id=?',cityId);
Truncating (emptying) a table
You can empty a table of all its contents, but SQLite doesn't include the typical TRUNCATE command to do so. You use the DELETE command:

db.execute('DELETE FROM city'); // would empty the table!
Manipulating the Database's Structure
SQLite supports various commands for modifying an existing database. These include: DROP, ALTER, and the PRAGMA commands.

Dropping a table
Dropping a table removes it from the database. You can't recover it or undo your action once it's done. To prevent an error from being thrown if the table is already deleted, you can add the IF EXISTS clause:

db.execute('DROP TABLE IF EXISTS tablename');
Altering a table
SQLite supports a limited subset of the ALTER TABLE command. You can add columns or rename a table. But, you cannot remove or rename a column from a table. These are both supported commands:

ALTER tablename ADD COLUMN column_definition
ALTER tablename RENAME TO newname – to rename a table
(To be clear, not being able to remove or rename columns is a SQLite limitation, not a Titanium limit.)
PRAGMA commands
The PRAGMA commands provide SQLite-specific tools for determining the structure of the database and its tables and to set database operation parameters. Of these, only a few are useful in a mobile OS environment. These include table_info(), index_list(), integrity_check(), and so forth. See the SQLite site for the full list of commands.
Best Practices
We recommend the following as best practices:

Close database connections and resultsets as soon as you're done with them.
Batch inserts/updates by wrapping them in a transaction.
Don't ship a large pre-populated database with your app, download it instead
Store a version number in your database for easier app updating.
Close the database and resultset with each operation
In a mobile app, you're dealing with a single-user, memory constrained environment in which connection pooling is not important. The time to open/close connections is less of a hit that consuming memory by keeping connections open.

Furthermore, SQLite enforces sequential write-access to the database (one process at a time). This makes it vital that you close the database connection when you have completed any INSERT or UPDATE operations. If you don't, you might receive "DatabaseObjectNotClosed" exceptions when your script next attempts to write to it.

cityWeatherRS.close(); // close the resultset when you're done reading from it
db.close(); // and close the database connection
Use transactions to speed batch inserts
A (perhaps not so widely known) trick in the database world is to use transactions to speed up inserts. Let's say you have a loop that does ten inserts or updates. By wrapping them in a transaction, you create a single, mass operation against the database rather than ten little operations. The single operation can be significantly faster than those individual updates. The downside is that if any of the updates fail, the entire batch is rolled back.

// you get a bunch of data, maybe from the network, to save in the db
var playlist = [];
playlist.push({disc:'Leon Live', artist:'Leon Russell', comment:'Gospel, blues and rock rolled into one'});
playlist.push({disc:'Animal Notes', artist:'Crack the Sky', rating:'Obscure but rocking'});
// etc.
var db = Ti.Database.open('myDatabase');
db.execute('BEGIN'); // begin the transaction
for(var i=0, var j=playlist.length; i < j; i++) {
var item = playlist[i];
db.execute('INSERT INTO albums (disc, artist, rating) VALUES (?, ?, ?)', item.disc, item.artist, item.comment);
}
db.execute('COMMIT');
db.close();
Use a minimal pre-populated database
Shipping a pre-populated database with your app increases the size of the app's package file (ipa or apk) making for slower downloads from the App Store or Market. It also consumes more storage space on the user's device. Because the Resources directory is read-only on the device, the distribution database file cannot be deleted, resulting in two copies of this file on the user's device.

Rather than shipping a large pre-populated database, you can ship a "skeleton" database file instead. This would be a database with the minimum amount of data required for the application to run. Then, on first boot, ask the user's authorization to download a replacement from a remote source, using something like the following method:

var updateMyDatabase = function(newdata) {
// logic here to delete existing content and insert new data
// maybe you've retrieved a bunch of JSON encoded data that you
// rehydrate, loop through, and insert into your tables
}
buttonInstallRemote.addEventListener('click', function(){
var c = Ti.Network.createHTTPClient();
c.setTimeout(10000);
c.onload = function(e){
updateMyDatabase(this.responseData);
Ti.UI.createAlertDialog({title:'Info', message:'Database installed', buttonNames: ['OK']}).show();
};
c.onerror = function(e){
Ti.UI.createAlertDialog({title:'Error', message:'Error: ' + e.error, buttonNames: ['OK']}).show();
};
c.open('GET',"http://example.com/getNewDatabaseData);
c.send();
});
Store a version number to ease database updates
By storing a version number within your database, you can detect which version is installed and take action as needed. For example, your app might determine that version 1.0 of your database is installed, then create new tables or alter existing tables. However, if you don't have a version identifier in your database, you'll be left guessing...is the user really running the old version or did a previous update fail? Are they downloading a new app version or upgrading an existing version? Whatever the scenario, we recommend you include a "version" table in your database in which you store a database version number.

If you've failed to do this, you might be able to use a PRAGMA command like the following to detect if a certain column is present and by doing so differentiate between an old and new version of your database.

// ADD COLUMN TO A TABLE
var addColumn = function(dbname, tblName, newFieldName, colSpec) {
var db = Ti.Database.open(dbname);
var fieldExists = false;
resultSet = db.execute('PRAGMA TABLE_INFO(' + tblName + ')');
while (resultSet.isValidRow()) {
if(resultSet.field(1)==newFieldName) {
fieldExists = true;
}
resultSet.next();
} // end while
if(!fieldExists) {
// field does not exist, so add it
db.execute('ALTER TABLE ' + tblName + ' ADD COLUMN '+newFieldName + ' ' + colSpec);
}
db.close();
};
Hands-on Practice
Goal
In this activity, you will write an app that will display the contents of a database in a table. The app's output will respect the user's preference for units based on data saved in a Ti.App.Properties property. When complete, your app should look like the following: images/download/attachments/29004901/Screen_shot_2011-08-16_at_9.32.25_AM.png
Resources
To perform the steps in this activity, you will need the following files:

The sample database, weather.sqlite.
5.2 Lab Code, finished state
Steps
If you didn't complete the 5.2 lab, download the starting code sample and import that project.
Download the sample database and store it in the Resources directory of your project.
Add the code to install the database, naming your app's database weather.
Add the following function to convert temperatures to Fahrenheit based on the user's preference (update as necessary to reflect your app's namespace):

myapp.convertTemp = function(temp) {
if(Ti.App.Properties.getString('units','c')==='f') {
return Math.round(temp*1.8+32) +'\u00b0F'; // convert to Fahrenheit & append degree symbol-F
} else {
return temp +'\u00b0C';
}
};
Write a function named getRows() that accomplishes the following:

Opens the database
Executes the following query: SELECT city, temp, condition, icon FROM cities JOIN conditionCodes ON cities.conditionID = conditionCodes.conditionID ORDER BY city
Loop through the result set. For each result, create a table row that contains these three child views:

An ImageView pointing to http://www.worldweather.org/img_cartoon/ plus the value of the icon column. The image should be shown at the left edge of the row.
Label to display the City name. The text should be large and bold, and positioned to the right of the image.
A Label to display the temperature, as formatted by the convertTemp() function. It should be smaller text, positioned at the right edge of the row.
Make sure to close the database connection when the function is done, and return the resulting array to the caller.
In the window on tab 1, add a TableView. Then write a populate() function that sets the table's data to the results from the getRows() function.
Add an App-level event listener that calls the populate() function.
Update the event listener on the units switch (on the preferences tab) to fire your app-level event when the user updates the temperature units.
Build and test your app in the simulator/emulator.
References and Further Reading
API docs: Ti.Database
the SQLite Official Homepage
Distinctive Features of SQLite
the SQLite Official Getting Started guide
Finished code
Summary

In this chapter, you learned how to interact with the built-in SQLite3 RDMS with the Ti.Database module. You learned how to create, install, and populate databases. You also learned how to use SQL to perform CRUD (create, read, update, and delete) operations on your data.
Created with Scroll EclipseHelp Exporter for Confluence.