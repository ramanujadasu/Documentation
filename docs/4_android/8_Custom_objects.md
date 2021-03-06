<span id="Overview" class="on_page_navigation"></span>
# Overview
Via Custom Objects module of QuickBlox Android SDK you can create any server side data structure, use it according to your needs, 
create any logic and a lot of other custom features.

In this guide you will learn how to:
* Create own server data structure & use it. In our example we created data structure that represents simple Note.
* Get, Create, Update, Delete your data using a lot of filters.

<span id="Prepare_your_application_for_QuickBlox_Android_SDK" class="on_page_navigation"></span>
# Prepare your application for Android SDK
This guide implies that you are already familiar with the procedure of QuickBlox framework integration and you have 
already taken the following steps for your application:

* [Created QuickBlox account](http://admin.quickblox.com/register)
* [Registerer an application in Dashboard](http://quickblox.com/developers/5_Mins_Guide)
* [Integrated QuickBlox SDK into application - stub link]()


<span id="Integrate_Custom_Objects_module_in_your_application" class="on_page_navigation"></span>
# Integrate Custom Objects module of QuickBlox Android SDK in your application
To use Custom Objects module in your app, you just need to add dependency in **build.gradle** project file:
```groovy
dependencies {
    compile "com.quickblox:quickblox-android-sdk-customobjects:3.3.3"
}
```


<span id="Add_Custom_Data_structure_to_your_application" class="on_page_navigation"></span>
# Add Custom Data structure to your application
To start using Custom Objects module you should create your data schema.
Go to **[Admin panel](http://admin.quickblox.com) -> Custom Objects module page** and press **Add new class** button. 
**Add new class** popup will appear.

Enter **Class name**, add fields you need. The following 7 types of fields are allowed:
* Integer
* Float
* Boolean (true/false)
* String
* File
* Location
* Array (of Integers, Floats, Booleans, Strings)

![](./resources/custom_objects/COCreateClass.png)

Press **Create class** button to create a new class.

![](./resources/custom_objects/COPredefinedFields.png)

There are some predefined fields given in [Custom Objects REST API Module description](http://quickblox.com/developers/Custom_Objects#Module_description)  documentation.

<span id="Create_record" class="on_page_navigation"></span>
# Create record 
There are 2 ways to create a record:
* through Admin panel
* using Android SDK


## Create record through Admin panel
Just go to [admin.quickblox.com](http://admin.quickblox.com) or your Admin Panel link (for enterprise customers), 
**Custom Objects module** page and press **Add record** button. **Add new record** popup 
will appear. Fill in any fields you need and press **Add record** button. New record will be added & shown in table.


## Create a record using QuickBlox Android SDK
To create a record use the snippet below:

```java
QBCustomObject object = new QBCustomObject();

// put fields
object.putString("name", "Star Wars");
object.putString("comment", "Star Wars is an American epic space opera franchise consisting of a film series created by George Lucas.");
object.putString("genre", "fantasy");

// set the class name
object.setClassName("Movie");

QBCustomObjects.createObject(object).performAsync(new QBEntityCallback<QBCustomObject>() {
    @Override
    public void onSuccess(QBCustomObject qbCustomObject, Bundle bundle) {
    
    }

    @Override
    public void onError(QBResponseException e) {

    }
});
```


<span id="Retrieve_records" class="on_page_navigation"></span>
# Retrieve records

## By ID
To retrieve a record by id use the following code snippet:
```java
QBCustomObject customObject = new QBCustomObject("Note", "50e3f8c7535c126073000d52");

QBCustomObjects.getObject(customObject).performAsync(new QBEntityCallback<QBCustomObject>() {
    @Override
    public void onSuccess(QBCustomObject qbCustomObject, Bundle bundle) {
    
    }

    @Override
    public void onError(QBResponseException e) {

    }
});
```

## By IDs
To retrieve records by ids use the following code snippet:
```java
StringifyArrayList<String> coIDs = new StringifyArrayList<String>();
coIDs.add("50e67e6e535c121c66004c74");
coIDs.add("50e67e6d535c127f66004f47");
coIDs.add("50e67e6b535c121c66004c72");
coIDs.add("50e59f81535c121c660015fd");

QBCustomObjects.getObjectsByIds("Note", coIDs).performAsync(new QBEntityCallback<ArrayList<QBCustomObject>>() {
    @Override
    public void onSuccess(ArrayList<QBCustomObject> qbCustomObjects, Bundle bundle) {
    
    }

    @Override
    public void onError(QBResponseException e) {

    }
});
```


## With filters
There are lots of operators and filters that you can use to retrieve records:

### Filters
The request can contain all, some or none of the following filters.


Filter | Applicable to types | Usage example | Description
-------|---------------------|---------------|------------
|**{field_name}** |All types  | ```requestBuilder.eq("rating", "2");``` | Search records containing specified value
|**{field_name}[{search_operator}]** | All types  | ```requestBuilder.gt("rating", "5");``` | Search record with field containing specified value and operator
|**sort_asc** | All types  | ```requestBuilder.sortAsc("field_name");``` | Search results will be sorted by specified field in ascending order
|**sort_desc** | All types  | ```requestBuilder.sortDesc("field_name");``` | Search results will be sorted by specified field in descending order
|**skip** | Integer | ```requestBuilder.setSkip(100);``` | Skip N records in search results. Useful for pagination. Default (if not specified): 0
|**limit** | Integer | ```requestBuilder.setLimit(50);``` | Limit search results to N records. Useful for pagination. Default value - 100. Also you can set 1000.**If limit is equal to -1 only last record will be returned**
|**count** | Integer | There is a separate method to count records | Count search results. Response will contain only count of records found
|**output[include]** | All types | ```requestBuilder.outputInclude(arrayOfFields);``` | The **output** parameter takes the form of a record with a list of fields for inclusion or exclusion from the result. (не понял, что надо поменять, в исправлении тоже самое) **output[include]** specifies the fields to include. The **_id** field is included in the result by default. To exclude the **_id** field from the result, you need to specify the exclusion of the **_id** field in the **output[exclude]** value.
|**output[exclude]** | All types | ```requestBuilder.outputExclude(arrayOfFields);``` | The **output** parameter takes the form of a record with a list of fields for inclusion or exclusion from the result. **output[exclude]** specifies the fields to exclude. The **_id** field is included in the result by default. To exclude the **_id** field from the result, you need to specify the exclusion of the **_id** field in the **output[exclude]** value.
|**near** | Location | ```requestBuilder.near("user_location", new Double[]{34.666, 12.445}, 10);``` |  Search records in a specific radius around current position in meters. Format: field_name,longitude,latitude,radius(in meters)


### Search operators 

The request can contain all, some or none of the following search operators. Combinations of operators are allowed.

Operator | Applicable to types | Usage example | Description
---------|---------------------|---------------|-------------
| **lt** | Integer, Float | ```requestBuilder.lt("rating", 5);``` | **L**ess **T**han operator
| **lte** | Integer, Float | ```requestBuilder.lte("rating", 5);``` | **L**ess **T**han or **E**qual to operator
| **gt** | Integer, Float | ```requestBuilder.gt("rating", 5);``` | **G**reater **T**han operator
| **gte** | Integer, Float | ```requestBuilder.gte("rating", 5);``` | **G**reater **T**han or **E**qual to operator
| **ne** | Integer, Float, String, Boolean | ```requestBuilder.ne("rating", 5);``` | **N**ot **E**qual to operator
| **in**| Integer, Float, String,Array | ```requestBuilder.in("tags", "man", "golf");``` | Contained **IN** array operator
| **nin** | Integer, Float, String,Array | ```requestBuilder.nin("tags", "man", "golf");``` | **N**ot contained **IN** array
| **all** | Array | ```requestBuilder.all("tags", "man", "golf");``` | **ALL** contained **IN** array
| **or** | Integer, Float, String | 1.```requestBuilder.or("name", "igor", "bob");```<br>2.```requestBuilder.or("name", "igor").or("lastname", "khomenko");``` | 1.Returns records with name **igor** or **bob** <br>2.Returns records with name **igor** or lastname **khomenko**
| **ctn** | String | ```requestBuilder.ctn("username", "ar");``` | Returns all records where **username** field contains **ar** substring


For example, we want to retrieve 5 movies with the following parameters:
* Have **rating > 5.5**
* Not documentary
* Movies must be sorted by **rating** field in ascending order 

```java
QBRequestGetBuilder requestBuilder = new QBRequestGetBuilder();
requestBuilder.gt("rating", "5.5");
requestBuilder.setLimit(5);
requestBuilder.eq("documentary", "false");
requestBuilder.sortAsc("rating");

QBCustomObjects.getObjects("Movie", requestBuilder).performAsync(new QBEntityCallback<ArrayList<QBCustomObject>>() {
    @Override
    public void onSuccess(ArrayList<QBCustomObject> customObjects, Bundle params) {
        int skip = params.getInt("skip");
        int limit = params.getInt("limit");
    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```


<span id="Update_record" class="on_page_navigation"></span>
# Update record
To update an existing record you need to know the record **ID**. 
Let's update Movie with **ID 502f7c4036c9ae2163000002** and set its **rating** to **7.88**:

```java
QBCustomObject record = new QBCustomObject();
record.setClassName("Movie");
HashMap<String, Object> fields = new HashMap<>();
fields.put("rating", "7.88");
record.setFields(fields);
record.setCustomObjectId("502f7c4036c9ae2163000002");

QBCustomObjects.updateObject(record, null).performAsync(new QBEntityCallback<QBCustomObject>() {
    @Override
    public void onSuccess(QBCustomObject object, Bundle params) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```

Also there are some very helpful Special update operators used for such purposes as updating Array field, for example:

## Special update operators

Operator | Applicable to types | Usage example | Description
---------|---------------------|---------------|-------------
| inc | Integer, Float | ```updateBuilder.inc("rating", 1);``` | Increment field to specified value (приплюсовать к текущему значению указанное). Value can be positive or negative (i.e. decrement operation)
| pull | Arrays | ```updateBuilder.pull("tags", "man");``` | Removes specified value from array field 
| pull **with filter** | Arrays | ```updateBuilder.pullWithFilter("user_ids", "gt", 10);``` | Removes all elements, which are filtered by filter operator, from array
| pull_all | Arrays | ```updateBuilder.pullAll("tags", "one", "two");``` | Removes all specified values from array
| pop | Arrays | ```updateBuilder.pop("tags", 1);``` | Removes last element from array. To remove first element value should be equal to **-1**
| push | Arrays | ```updateBuilder.push("tags", "one", "two");``` | Appends specified values to array
| add_to_set | Arrays | ```updateBuilder.addToSet("tags", "man");``` | Adds a value to an array only if the value is not in the array already
| Update array element by index operator | Arrays | ```updateBuilder.updateArrayValue("tags", 1, "two");``` | Update array element by index


Let's update an element with index 1 in array **tags** with value **man**:
```java
QBCustomObject record = new QBCustomObject();
record.setClassName("Movie");
record.setCustomObjectId("502f7c4036c9ae2163000002");

QBRequestUpdateBuilder updateBuilder = new QBRequestUpdateBuilder();
updateBuilder.updateArrayValue("tags", 1, "man");

QBCustomObjects.updateObject(record, updateBuilder).performAsync(new QBEntityCallback<QBCustomObject>() {
    @Override
    public void onSuccess(QBCustomObject object, Bundle params) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```


<span id="Delete_record" class="on_page_navigation"></span>
# Delete record
To delete an existing record you **need to know record ID**. Let's delete Movie with ID **502f83ed36c9aefa62000002**:
```java
QBCustomObject customObject = new QBCustomObject("Movie", "502f83ed36c9aefa62000002");

QBCustomObjects.deleteObject(customObject).performAsync(new QBEntityCallback<Void>(){
    @Override
    public void onSuccess(Void aVoid, Bundle bundle) {

    }

    @Override
    public void onError(QBResponseException e) {

    }
});
```


<span id="Multi_operations" class="on_page_navigation"></span>
# Multi operations
It is possible to do some operations on multiple objects in a single query.

## Create multiple records
Create multiple records in a single query:
```java
// Create 3 random records
List<QBCustomObject> customObjectsList = new ArrayList<QBCustomObject>(3);

for (int i = 0; i < 3; i++){
    Random random = new Random();
    QBCustomObject customObject = new QBCustomObject("Note");
    customObject.putInteger("rating", random.nextInt(100));
    customObject.putString("description", "Hello world");
  
    customObjectsList.add(customObject);
}

QBCustomObjects.createObjects(customObjectsList).performAsync(new QBEntityCallback<ArrayList<QBCustomObject>>() {
    @Override
    public void onSuccess(ArrayList<QBCustomObject> customObjects, Bundle args) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```

## Update multiple records
Update multiple records in a single query:
```java
QBCustomObject co1 = new QBCustomObject("Note");
co1.putInteger("rating", 10);
co1.setCustomObjectId("50e67e6d535c127f66004f47");

QBCustomObject co2 = new QBCustomObject("Note");
co2.putInteger("rating", 8);
co2.setCustomObjectId("50e67e6b535c121c66004c72");

QBCustomObject co3 = new QBCustomObject("Note");
co3.putInteger("rating", 12);
co3.setCustomObjectId("50e59f81535c121c660015fd");

List<QBCustomObject> customObjectList = new LinkedList<>();
customObjectList.add(co1);
customObjectList.add(co2);
customObjectList.add(co3);

QBCustomObjects.updateObjects(customObjectList).performAsync(new QBEntityCallback<ArrayList<QBCustomObject>>() {
    @Override
    public void onSuccess(ArrayList<QBCustomObject> objects, Bundle params) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```

## Delete multiple records
Delete multiple records in a single query:
```java
StringifyArrayList<String> deleteIds = new StringifyArrayList<>();
deleteIds.add("50e3f85f535c123376000d31");
deleteIds.add("50e3f85f535c123376000d32");

QBCustomObjects.deleteObjects("Note", deleteIds).performAsync(new  QBEntityCallback<ArrayList<String>>() {
    @Override
    public void onSuccess(ArrayList<String> deletedObjects, Bundle params) {
        Log.i(TAG, ">>> deleted: " + deletedObjects.toString());
                
        ArrayList<String> notFound = params.getStringArrayList(com.quickblox.customobjects.Consts.NOT_FOUND_IDS);
        Log.i(TAG, ">>> notFound: " + notFound.toString());

        ArrayList<String> wrongPermissions = params.getStringArrayList(com.quickblox.customobjects.Consts.WRONG_PERMISSIONS_IDS);
        Log.i(TAG, ">>> wrongPermissions: " + wrongPermissions.toString());
    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```


<span id="Relations" class="on_page_navigation"></span>
# Relations
We allow to organize relation between 2 objects - just use special field **_parent_id**. 

For example, if you want to add Comments for a movie, you can use code snippet below:

```java
QBCustomObject customObject = new QBCustomObject("Comment"); // your Class name
customObject.putInteger(fieldHealth, 99);
customObject.putString("text", "The first film in the series was originally released on May 25, 1977, under the title Star Wars, by 20th Century Fox");
customObject.setParentId("50aa4d8fefa357fa14000001");

QBCustomObjects.createObject(customObject).performAsync(new QBEntityCallback<QBCustomObject>() {
    @Override
    public void onSuccess(QBCustomObject object, Bundle params) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```

Such relation is a strong reference - it means that if you will delete the parent record (Movie), all child records 
(Comments) will be deleted too.

This field (```_parent_id```) may be included in Retrieve/Create/Update queries as well.


<span id="Permissions" class="on_page_navigation"></span>
# Permissions
Permissions (access control list)  is a list of  permissions attached to a record in Custom Objects module. This list specifies which 
users are granted access to records, as well as what operations are allowed on given objects (**READ, CREATE, UPDATE, DELETE**).

Each entry in a typical Permissions list specifies a subject and an operation. For instance, if a record has a permission that contains (Garry, EDIT), 
this would give Garry permission to edit this record.

Read [Permissions REST API description](http://quickblox.com/developers/Custom_Objects#Permissions)  to understand how it works better.

There are 4 typical operations on record:
* CREATE
* READ
* UPDATE
* DELETE

```CREATE``` operations can't be set through API (only in Admin panel)

Each operation has info who can perform it on object.

There are 5 values (```QBPermissionsLevel```):
* ```QBPermissionsLevel.OPEN``` - any user can perform operation on this record
* ```QBPermissionsLevel.OWNER``` - only owner can perform operation on this record
* ```QBPermissionsLevel.NOT_ALLOWED``` - nobody can perform operation on this record (except Owner)
* ```QBPermissionsLevel.OPEN_FOR_USER_IDS``` - users with these IDs can perform operation on this record
* ```QBPermissionsLevel.OPEN_FOR_GROUPS``` - users in these groups can perform operation on this record

<div class="attention">
<b>Note:</b> Owner always has access to own record (except case when [Class permissions level](http://quickblox.com/developers/Custom_Objects#Permission_levels)  is enabled)

Подумаю, как обьяснить
</div>


## Create record with Permissions
Let's create a record with the following permissions:
* ```READ```: Open
* ```UPDATE```: Users in groups **golf, man**
* ```DELETE```: Users with IDs **3060, 63635**

```java
QBCustomObject newRecord = new QBCustomObject("Note");
newRecord.put("rating", 99);
newRecord.put("description", "Hello world");

// set permissions:
// READ
QBPermissions permissions = new QBPermissions();
permissions.setReadPermission(QBPermissionsLevel.OPEN);

// UPDATE
ArrayList<String> usersTags = new  ArrayList<>();
usersTags.add("golf");
usersTags.add("man");
permissions.setDeletePermission(QBPermissionsLevel.OPEN_FOR_GROUPS, usersTags);

// DELETE
ArrayList<String> usersIDS = new  ArrayList<>();
usersIDS.add("3060");
usersIDS.add("63635");
permissions.setDeletePermission(QBPermissionsLevel.OPEN_FOR_USER_IDS, usersIDS);

newRecord.setPermission(permissions);

QBCustomObjects.createObject(newRecord).performAsync(new QBEntityCallback<QBCustomObject>() {
    @Override
    public void onSuccess(QBCustomObject object, Bundle params) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```

## Obtain record's Permissions
You can obtain info about record permissions by its ID - info only about own records are available for a user: 

```java
QBCustomObjects.getObjectPermissions("Note", "53f44e7befa3573473000002").performAsync(new QBEntityCallback<QBPermissions>() {
    @Override
    public void onSuccess(QBPermissions permissions, Bundle params) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```

## Update record's Permissions
Let's update record's permissions to the following:
* ```READ```: Users in groups **car, developers**
* ```UPDATE```: Owner
* ```DELETE```: Owner

```java
QBCustomObject record = new QBCustomObject();
record.setClassName("Note");
record.setCustomObjectId("52b30274535c12fbf80121bd");

// update permissions:
// READ
QBPermissions permissions = new QBPermissions();
ArrayList<String> usersTags = new  ArrayList<>();
usersTags.add("car");
usersTags.add("developers");
permissions.setDeletePermission(QBPermissionsLevel.OPEN_FOR_GROUPS, usersTags);

// UPDATE
permissions.setUpdatePermission(QBPermissionsLevel.OWNER);

// DELETE
permissions.setDeletePermission(QBPermissionsLevel.OWNER);

record.setPermission(permissions);

QBCustomObjects.updateObject(record, null).performAsync(new QBEntityCallback<QBCustomObject>() {
    @Override
    public void onSuccess(QBCustomObject object, Bundle params) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```


<span id="Files" class="on_page_navigation"></span>
# Files
Custom Objects module supports ```File``` field type. It is created for easily working with content from Custom Objects module. 
There is an ability to upload, download, update and delete content of file fields.

<div class="attention">
Note: The <b>max</b> file size is <b>32 MB</b>.
</div>

## Upload/Update file
```java
// get file
File file = ...;

QBCustomObject customObject = new QBCustomObject("Note", "a234234abc647fed6473333");

QBCustomObjectsFiles.uploadFile(file, customObject, "avatar", new QBProgressCallback() {
    @Override
    public void onProgressUpdate(int progress) {

    }
}).performAsync(new QBEntityCallback<QBCustomObjectFileField>() {
    @Override
    public void onSuccess(QBCustomObjectFileField uploadFileResult, Bundle params) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```

## Download file
```java
QBCustomObject customObject = new QBCustomObject("Note", "a234234abc647fed6473333");

QBCustomObjectsFiles.downloadFile(customObject, "avatar", new QBProgressCallback() {
    @Override
    public void onProgressUpdate(int progress) {

    }
}).performAsync(new QBEntityCallback<InputStream>(){
    @Override
    public void onSuccess(InputStream inputStream, Bundle params) {

    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```

## Delete file
```java
QBCustomObject customObject = new QBCustomObject("Note", "a234234abc647fed6473333");

QBCustomObjectsFiles.deleteFile(customObject, "avatar").performAsync(new QBEntityCallback<Void>() {
    @Override
    public void onSuccess(Void aVoid, Bundle bundle) {
                
    }

    @Override
    public void onError(QBResponseException errors) {

    }
});
```

<span id="Sources" class="on_page_navigation"></span>
# Sources
Project homepage on GIT - https://github.com/QuickBlox/quickblox-android-sdk/tree/master/sample-custom-objects

Download ZIP - https://github.com/QuickBlox/quickblox-android-sdk/archive/master.zip
