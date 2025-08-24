



# Mongodb


## mongodb vs sql performance

1. less complexity vs relational db
2. esp when sql involes joins in multiple tables
3. easiter to maintain
4. do not need to manage relationships between multiple tables
5. easy to scale


## Mongodb environment

1. MongoDB Atlas       (for deployment in the cloud)
2. MongoDB Enterprise
3. MongoDB Community  (free-to-use)


###  download .exe files (Community version)

1. mongo shell
2. mongo compass
3. MongoDB Command Line Database Tools


### MongoDB Atlas 

+ MongoDB Atlas is a cloud-based db service
+ Integration with tools like MongoDB Compass by giving us an URI
+ we can use it with cloud providers like AWS, GCP, Azure
+ Mongo Atlas offers: 1. monitoring and alerts 2. security 3. auto scaling 4. auto backups
+ MongoDB Atlas is free. pricig: 
    - storage:512 MB, shared cpu, shared RAM


#### 1. Mongodb Atlas intallation

1. Account & Cluster Setup
    a. Sign up for MongoDB Atlas
    b. Create a New Project
    c. Build a Cluster
    d. Cloud Provider (AWS, GCP, Azure)
    e. Region (Pick closest to users)
    f. Choose Cluster Tier (Free: storage:512 MB, shared cpu, shared RAM)
2. Database Access & Security
    a. Go to Database Access → Add New User
    b. Authentication Method (Password/AWS IAM/ this password is diffrent from account)  
    c. User Privileges (Read-Write/dbAdmin)
    d. Configure Network Access -> Go to Network Access → Add IP Address
        - Your current IP 
        - 0.0.0.0/0 (can access from any IPs, not recommended for production)
3. Connect to Your Cluster
    a. click on Connect in 'data base view'
    b. choose your connection method
4. Data Migration (Optional)
    a. Import your data


#### 2. Mongodb Atlas UI
1. Or anization view : you can see a list of all your projcets
2. Datavase view




## Mongo concepts

1. Mongo stores data as `"field": "value"` inside document
2. these documents stored inside 'collections'
3. collections stored inside db

| SQL          | MongoDB                     |
| ------------ | --------------------------- |
| database     | database                    |
| table        | collection                  |
| row          | document                    |
| column       | field                       |
| primary key  | _id                         |
| index        | index                       |
| JOIN         | $lookup                     |
| foreign key  | manual reference            |
| SELECT query | find or aggregation         |
| INSERT       | insertOne / insertMany      |
| UPDATE       | updateOne / updateMany      |
| DELETE       | deleteOne / deleteMany      |
| WHERE        | filter object               |
| GROUP BY     | $group                      |
| ORDER BY     | sort                        |
| LIMIT        | limit                       |
| OFFSET       | skip                        |
| HAVING       | match after group           |
| TRANSACTION  | session with transaction    |


##### 0. Naming conventions

1. db         ----> snake_case or kebab-case ---> 
2. collection ---->  snake_case plural nouns ---> users, order_items
3. field      ----> camelCase           ------------> firstName, userId (not user_id)


##### 1. Schema

+ relational dbs have a schema
+ Schema is a fiexed structure that is defined beforehand
+ doc dbs are 'schema-less'
+ schema-less ecourages store data as much as posible inside db
+ documents are polimorphic
+ polymorphic means documents do not have a fixed structure.
+ changes can made easily to an individual document and documents easily adapt new changes like adding a new field value pairs


#### 2. document

+ docs in mongodb use Json-like format that represents a single record in a collection
+ docs actually stored in BSON

```js
{
  "_id": ObjectId("64e1f9cafe4b2c88e9eab63f"),
  "name": "John Doe",
  "age": 30,
  "skills": ["JavaScript", "MongoDB", "React"],
  "isActive": true,
  "createdAt": ISODate("2025-08-05T14:00:00Z")
}
```

#### 3. sub-document

```js
{
  "_id": ObjectId("64e1f9cafe4b2c88e9eab63f"),
  "name": "John Doe",
  "address": {                         // address is a sub-document
    "street": "123 Main St",
    "city": "Anytown",
    "zip": 12345
  }
}
```

#### 4. data types

 + BSON supports additional data types that aren't available in JSON
 + Helper functions let us define specific BSON data types

```js
{
  _id: ObjectId("64e1f9cafe4b2c88e9eab63f"),              // ObjectId
  stringField: "Hello, MongoDB",                          // String           //  BSON strings are UTF-8
  intField: NumberInt(42),                                // 32-bit Integer
  longField: NumberLong("9223372036854775807"),           // 64-bit Integer
  doubleField: 3.14159,                                   // Double
  decimalField: NumberDecimal("12345.6789"),              // Decimal128
  boolField: true,                                        // Boolean
  nullField: null,                                        // Null
  dateField: ISODate("2025-08-05T14:00:00Z"),             // Date
  timestampField: Timestamp(1623156123, 1),               // Timestamp
  arrayField: [1, "two", true, null],                     // Array
  embeddedDoc: {                                          // Object
    nestedString: "Nested value",
    nestedNumber: 10
  },
  binaryField: BinData(0, "VGhpcyBpcyBhIHRlc3Q="),        // Binary data (Base64)
  regexField: /mongo/i,                                   // Regular Expression
  jsCodeField: Code("function() { return true; }"),       // JavaScript code
  jsCodeWithScope: Code("function(x) { return x + y; }",  // JS code with scope
    { y: 5 }
  ),
  minKeyField: MinKey(),                                  // MinKey
  maxKeyField: MaxKey()                                   // MaxKey
}
```

#### 5. flexiblity in doc

```js
// colelction 1
{
    _id: 123,
    phone: [914, 913],
    first_name: "ali"
}
// collection 2
{                                // these two collections have diffrent key-value pairs
    _id: 124,
    phone: 912,                   // phone in colelction 1 is an array
    first_name: "ahmad",         // fields can store diff data types in diff collecitons due to flexiblity
    last_name: "javadi"          // collection 1 has no last_name
}
```



## mongodb deployment

+ mongodb is natively distributed system and very good at coordinating data across multiple servers 
1. replica set
2. sharded clusters

+ a replica set consists of 3 machines or 3 instances
+ each machine contains a complete replica of the data
+ a sharded cluster contains a group of replica sets
+ its a partition of the db which splits data across servers
+ a sharded cluster consists of the components:
    + 1. Shard: containing the partition of the data
    + 2. Mongos: mongo db's query router. acts as an interface between any application using mongodb and sharded cluster
    + 3. config servers: store meta data and configuration settings
+ mongodb use a `shard key` to distribute this collection across the shards
+ stategies of sharding:








## Mongodb database tools

```bash
mongoimport                               # Import JSON, CSV, or TSV into a collection
mongoexport                              # Export a collection to JSON, CSV, or TSV
mongodump                                 # Create a binary backup (BSON: binary JSON) of a database which is not human-readable
mongorestore                             # Restore a binary backup (BSON)
mongofiles
mongotop
mongostat
```

###### 1. URI

+ URI (Uniform Resource Identifier)
+ URI is a string of characters that identifies any resource (either by 'location' or 'name' or both)
+ URI can be:
    1. URL (Uniform Resource Locator): A subset of URI that specifies where a resource is available
    2. URN (Uniform Resource Name):    A subset of URI that gives a resource a persistent name (regardless of location)
+ URI example:
    - https://example.com (URL)
    - urn:isbn:0451450523 (URN)
    - mailto:user@example.com (Both)


###### 2. Export and Import

```bash
URI = mongodb+srv://user:pass@host:port/db_name           # get the URI from mongo atlas

mongoexport --uri="" --out=1.json                                                   # export the whole database
mongoexport --uri="" --collection=users --out=1.json                                 # export a collection of database
mongoexport --uri="" --collection=users --out=1 --type=json                          # specify json format
mongoexport --uri="" --collection=users --out=1.csv --fields=_id,first_name          # specify fields for csv format
mongoexport --uri="" --collection=users --out=1.json --query="{'first_name':'ali'}"   # filter docs
mongoexport --uri="" --collection=users --out=1.json --limit=10                      # limit the number of docs

mongoimport --uri="" --collection=users --file=1.json        
mongoimport --uri="" --collection=users --file=1.csv --type=csv --headerline         # headerline means the first line is a header

```

###### 3. Backup and Restore

```bash
mongodump --uri=""                                      # backup the whole database
mongodump --uri="" --collection=users                  # backup the whole collection
mongodump --uri="" --out=/myfolder/                    # default value for --out is "/dump/"
mongodump --uri="" --gzip                              # compress the backup

mongorestore --uri="" /myfolder/                      # restore from this folder
```



### data modeling considerations

- The right schema design can enable you to make the most out of your database



## Basic commands

```bash
mongosh                                                   # connect to mongodb shell locally
mongosh "mongodb://localhost:27017"                       # equivalent to 'mongosh'
mongosh --port 28015                                      # connect to a specific port
mongosh --host 192.168.1.109 --port 28015                  # connect to a specific host
mongosh --host 192.168.1.109 --port 28015 -u myusername -p mypassword         # Connect to remote MongoDB instance
mongosh db_name                                     # Connect to specific database like ecommerce_db
use db_name                                               # Switch/create database (connect to a db)
show dbs                                                  # list of all dbs
show collections                                          # list of all collections in current db
db                                                       # Show current database
db.dropDatabase()                                         # drop current database
```


### Manage collections

+ `db.createCollection(name, options)`
    + options are for validations

```py
db.createCollection('books')                         # create an empty collection with no validation
db.books.stats()                                     # get collection statistics
db.books.drop()                                      # drop a collection
```

#### a. Validation options

+ when you create a collection you can enforce rules on collection structure
+ This helps maintain data integrity
+ 
+ syntax for create  a colletion: `db.createCollection(name, options)`
    + options are for validations
    + in options we can specify 3 items for validation:
        1. validator 
        2. validation level 
            a. strict (default):      apply to all inserts and updates
            b. moderate:              apply only to inserts and updates to valid documents
        3. validation action
            a. error(default):        throw an error
            b. warn:                  log a warning

+ we can temporarily bypass validation for specific operations like insert: 
    `db.students.insertOne({}, { bypassDocumentValidation: true });`

###### 1. Json Schema Validation (Recommended)

```js
db.createCollection("users")                         // without any validation
db.createCollection("users", {                       // {} is the options
  validator: {
    $jsonSchema: {}
  },
  validationLevel: "strict",     // 'strict' or 'moderate'
  validationAction: "error"      // 'error' or 'warn'
});

db.createCollection("users", {                       // {} is the options
  validator: {
    $jsonSchema: {
      bsonType: "object",                                         // Must be a document
      required: ["name", "email", "age"],                          //These fields are mandatory
      properties: {                                               // Define rules for each field
        name: {                                                   // 1. name field
          bsonType: "string",
          description: "must be a string and is required"
        },
        email: {                                                 // 2. email field
          bsonType: "string",
          pattern: "^.+@.+$",
          description: "must be a valid email and is required"
        },
        age: {                                                  // 3. age field
          bsonType: "int",
          minimum: 18,
          maximum: 99,
          description: "must be an integer between 18 and 99"
        }
      }
    }
  }
});
```

##### 2. Expression-Based Validation (Pre-3.6)

```js
db.createCollection("products", {
  validator: {
    $and: [
      { price: { $gte: 0 } },
      { name: { $type: "string" } }
    ]
  }
});
```

##### 2. Add validation to existing collection

```js
db.runCommand({
  collMod: "students",               // collMod (collection modify)
  validator: {
    $jsonSchema: {
      // new schema definition
    }
  },
  validationLevel: "moderate",
  validationAction: "warn"
});
```

#### b. capped option

+ `Capped Collection`: capped collections are fixed-size. So they has a maximum size. Once the size is reached, new collections will be overwritten the old ones

```js
db.createCollection("logs", {       //// create a capped collection
  capped: true,       // default is false
  size: 1048576,     // 1 MB  //  required, in bytes
  max: 1000          // optional: max 1000 documents
});
```

#### c. collation option

+ collation refers to the set of rules that determine how string values arej compared and sorted

```js
db.createCollection("customers", {
  collation: {
    locale: "en",        // "en", "fr", "de", "fa"
    strength: 2  // 2 is Case-insensitive comparison         // Level of comparison (1 to 5)
  }              // check Strength Levels in mongo website
})
```



## MQL (Mongo Query Language)

+ A JSON-like language to interact with MongoDB and perform CRUD operations
+ MQL is schema-less

#### 1. insert

+ APIs: 1. insertOne, 2. insertMany, 3. insert
+ syntax: `db.collection.insert({}, options)`
+ options: 
    1. ordered:             stop on first error
        a. true (default)   do not continue 
        b. false:           try to insert all documents, skipping only the ones that cause errors
    2. writeConcertn         


```js
db.books.insertOne({"title":"habits", "number":45})                               // we can use insert or insertOne for one doc
db.books.insertMany([{"title":"habits", "number":45}, {"name":"public speaking", "number": 10}])          // insert multiple docs  [{},{},{}]
db.books.insert([{"title":"habits", "number":45}, {"name":"public speaking", "number": 10}])          // insert multiple docs  [{},{},{}]
db.books.insert({"title":"habits", "number":45})                                  // automatically create _id and collection if not exist
db.books.insert({"title":"habits", "number":45, "_id": 12345})                   // insert with Custom _id
db.books.insert({"title":"habits", "dateAdded": new Date()})                                  // current date
db.books.insert({"title":"habits"}, {bypassDocumentValidation: true})                                  // insert and skip validation
db.books.insert([{'title':1}, {'title':2}], {})
db.books.insertMany([                                // by default, if we get error, mongo stops inserting documents after an error
  { title: "Book A", _id:123 },          //  
  { title: "Book B", _id: 123 },                             // we have an error here for duplicate _id
  { title: "Book C", _id:125 }
], { ordered: false });                // do not stop, insert documents after an error

//  ---- return respone 
{
  acknowledged: true,
  insertedId: ObjectId("507f191e810c19729de860ea")
  // or for custom ID: insertedId: "special-book-123"
}
```

### 2. delete

+ APIs: 1. deleteOne, 2. deleteMany, 3. remove
+ syntax: `db.collection.deleteOne(filter, options)`
+ options:
    1. writeConcern
    2. collation:     specifies language-specific rules for string comparison
    3. hint           specific index for the delete operation

```js
db.books.drop();                                // [avoid this] completely removes the collection and all its documents (returns true or false)
db.getCollectionNames().includes("books");                    // Check if Collection Still Exists

db.books.deleteOne({"title":"choice theory"})                        // delete first match
db.books.deleteMany({"title":"choice theory"})                        // delete all filter matched
db.books.deleteMany({})                                  // delete all docs
db.books.remove({})                                      // delete all docs
db.books.deleteMany(
  { price: { $lt: 5 } }, // Cheap books
  { writeConcern: { w: "majority", wtimeout: 5000 } } // Ensure replication
);
db.books.deleteMany({ title: "1" },{ collation: { locale: "en", strength: 2 } });       // Case-insensitive delete

//  ---- return respone 
{
  acknowledged: true, // If write concern was used
  deletedCount: 5     // Number of documents deleted
}
```

#### 3. update

+ APIs: 1. updateOne, 2. updateMany 3. update
+ syntax: `db.collection.update(filter, update, options)`
+ options:
    1. writeConcern
    2. collation     
    3. hint           
    4. upsert:           insert doc if not exist. default: false
    5. arrayFilters      updates specific elements in an array 
    6. multi:            update multiple documents. default: false (update one document) [for insert() API]

+ update operators:
    1. $set
    2. $unset
    3. $rename
+ Arithmetic update operators:
    1. $inc
    2. $mul
    3. $max
    4. $min
+ Array Update Operators:
    1. $push
    2. $pull
    3. $pop
    4. $addToSet
    5. $each

```js
// 1. update operators
db.books.updateOne({"title": "no name"},{$set:  {'number':0}})            // update the first match doc
db.books.updateMany({"title":'no name'},{$set: {'number':0}})                    // update all filtered docs
db.books.updateMany({"title":'no name'},{$set: {'number':0, 'price':0}})                    // update multiple fileds
db.books.updateMany({},{$set: {'number':0}})                                   // update all docs
db.books.updateMany({},{$set: {'new_field':0}})                    // we can add a new field to all documents using update
db.books.updateMany({},{$unset: 'new_field': ''})                    // drop the field in all docs
db.books.updateMany({},{$rename: 'author': 'writer'})                    // rename the field

// 2. Arithmetic update operators
db.books.updateMany({},{$inc: {'price': 100}})                    // increment the price field by 100

// 3. Array Update Operators
db.books.updateMany({},{$push: {'tags': 'drama'}})                    // add an item to array of tags

//// options
db.books.updateMany({'title': '0'},{$set: {'price': 100}},{upsert:true})                    // update or insert if not found
db.books.updateMany({'title': '0'},{$set: {'price': 100}},{bypassDocumentValidation: false})                    // skip validation
db.books.updateMany({'title': '0'},{$set: {'price': 100}},{writeConcern: {w:1}})                    // 
```



#### 4. Combined Actions

+  multiple actions in a single atomic operation
    1. findOneAndUpdate()
    2. findOneAndReplace()
    3. findOneAndDelete()

+ options:
    1. writeConcern
    2. collation
    3. returnDocument: 
        a. "after"                     returns the updated doc
        b. "before" (default)           returns the original doc

```js
db.books.findOneAndUpdate(
  { title: "1984" },
  { $set: { lastUpdated: new Date() } },
  { returnDocument: "after" }             // Returns the updated doc
);
```


#### 5. Common Options for CRUD

####### 1. writeConcern

+ Controls acknowledgment level for write operations like delete, insert, and update
+ It defines how safe or durable a write should be
+ it guarantees that data is safely written
+ we use writeConcern to balance between performance and reliability
+ options:
    1. w                      (Write Acknowledgment) specifies how many nodes must acknowledge the write
        - 0                  No acknowledgment (fire-and-forget)
        - 1 (default)        primary node only
        - <number>           specific number of nodes
        - "majority"         majority of nodes
    2. j                      (Journaling) enable journal logging
        - true               wait for the write to be commited to the on-disk journal before acknowledging the write operation
        - false (default)    acknowledge after writing to memory (faster)
    3. wtimeout               maximum time to wait for write concern in milliseconds

```js
{ writeConcern: { w: 0 } }                           // Fire-and-forget (Unacknowledged)
{ writeConcern: { w: "majority", wtimeout: 5000 } }  // high durability and medium performance
{ writeConcern: { w: 4, j: true, wtimeout: 5000 } }   // highest durability and lowest performance
```

####### 2. collation

+ specify language-specific rules for string comparison
+ options:
    1. locale
    2. strength

```js
{ collation: { locale: "en", strength: 2 } }          // Case-insensitive
```

####### 3. comment

+ add a comment to the query

```js
{ comment: "User registration update" }
```

#### 6. Bulk Operations ??????????




#### 7. find query

+ APIs: 1. find, 2. findOne
+ syntax: `db.collection.find(query, projection)`
+ filter in atlas dashboard does not need to write : db.collection.find()


```py
db.books.findOne()                                       # return the first document in books
db.books.findOne({"title": "math"})                      # return first match
db.books.find()                                          # return all the docs, see 20 items on the screen, press 'it'
db.books.find({})                                        # return all the docs
db.books.find({"title": "math"})                            # filtering with key-value
db.books.find({"title": "math", "number":12})                            # multiple condition
db.books.find({"author.first_name": "ali"})                            # embeded field using dot notation   # author is an object
db.books.find({'title': $author})                               # we can use $ for accessing field valies


# projection
db.books.find({},{"title":1})                          # _id is inclieded by default
db.books.find({},{"title":1, "_id":0})                 # exclude _id
db.books.find({},{"title":1, "number":1})              # just return these fields, not all of fields
db.books.find({},{"new_filed":  "CONST"})                          # add a new field with value 'CONST' to all docs
```


#### 8. Query Operators

+ query operators --vs-- expression operators
+ `$` is used for operators like `$lt`
+ 
+ there are 2 type of syntax
    1. field-specific operators (field become first): `{field: {$operator: value}}` 
        - like: $lt, $gt, $in, $regex, $all, $size, 
        - These operators describe a condition for a specific field's value.
        - The syntax reflects a hierarchy:   field → operator → criteria.
    2. top-level operators (operator become first): `{$operator : []}`  or `{$operator: {}}`
        - like: $or, $and, $not, $nor, $inc, $push, $set 
    3. Aggregation Operators

##### 8.1 Comparison Operators

- syntax: `{ field: { $lt: value } }`
- syntax in $expr is diffrent: `{ $expr: { $lt:[] } }`
- operators:
    1. $eq, $ne
    2. $gt, $gte
    3. $lt, $lte

```js
db.books.find({"price": {$lt: 50}})
db.books.find({"date": {$gt: ISODate('2019-01-01')}})
{"salary": {$eq:50}}             // equal to (default) // requivalent to {"salary": 50}
{"salary": {$ne:50}}             // not equal
{"salary": {$gt:50}}             // greater than
{"salary": {$gte:50}}            // greater than or equal
{"salary": {$lt:50}}             // less than
{"salary": {$lte:50}}            // less than or equal
{"salary": { $in: [50, 100] } }  // In array, find documents with salary either 50 or 100
{"salary": { $nin: [50, 100] } } // Not in array, Anti-Pattern
{role: {$in: ["admin", "editor"]}}
```

##### 8.2 Logical Operators

- operators:
    1. $and
    2. $or, $nor
    3. $not             

```js
db.books.find({ $and: [{title: "math"}, {number: 12}] })                    // array of conditions  {$and: [{}, {}]}
db.books.find({ 'title': 'math', 'number': 12 })                           // same  // $and is default and implicitly applied
db.books.find({'title': 'math', {$or: [{number: 12}, {number: 13}] }})      // $or
db.books.find({"title":{$not:{$eq:'math'}}})                               // same as $ne   // we can combine operators

{ $and: [ {cond1}, {cond2} ] }                               // gets array of conditions { $and: [] }
{ $or:  [ {cond1}, {cond2} ] }
{ $nor: [ {cond1}, {cond2} ] }
{ $not: {condition} }                                     // { field: { $not: { <operator-expression> } } }

//if you use the same field condition without $and, it does not apply implicitly bcz first condition is overwritten by the second 
db.books.find({"number":{$lt:400},"number":{$gt:300}})   
db.books.find($and: [{"number":{$lt:400},"number":{$gt:300}}])             // solution 1: $and
db.books.find({'number': {$le:400, $gt:300}})                              // solution 2: shorthand syntax
```


##### 8.3 Array Operators

- operators:
    1. $all
    2. $in, $nin
    3. $elemMatch  ---> good for array of objects
    4. $size

* `$elemMatch` for array of objects is used to find docs that contain at least one object (among all objects in the array) matches the query criteria

```js
{ arrayField: { $all: [val1, val2] } }                       // Contains all
{ arrayField: { $in: [val1, val2] } }                       // Contains one of them
{ arrayField: { $elemMatch: { query1, query2, ... } } }      // Find element of array that matches multiple conditions
{ arrayField: { $size: 3 } }                                // Array size
// tags is an array of strings                          // sponsers is an array of objects like [{name:'Google', budget:45}]
db.books.find({"tags":{$all: ['drama', 'sci-fi']}})                   // $all means the array field should have all of these values with no order
db.books.find({"tags":{$size: 10 }})                                                // $size for length of array
db.books.find({"tags": { $not: {$size: 0 }})                                                // find not empty arrays
db.books.find({"tags":{$elemMatch: {$in: ['drama', 'sci-fi']} }})                   // condition on array of strings
db.books.find({"sponsers":{$elemMatch : {"name":'Google', 'budget':{$gt:80}}})      // condition of array of objects

db.books.find({"tags":['drama','sci-fi']})               // return books that have tags with this order('drama' first and 'sci-fi' in second)       
db.books.find({"sponsers.name":"Google")                 // use dot notation to access array of objects
```

##### 8.4 element operator

- operators:
    1. $exists
    2. $type

- Common BSON Type Numbers:
    | Type Name  | Number |
    | ---------- | ------ |
    | double     | 1      |
    | string     | 2      |
    | object     | 3      |
    | array      | 4      |
    | binary     | 5      |
    | objectId   | 7      |
    | bool       | 8      |
    | date       | 9      |
    | null       | 10     |
    | regex      | 11     |
    | int32      | 16     |
    | timestamp  | 17     |
    | int64      | 18     |
    | decimal128 | 19     |

```js
{ field: { $exists: true } }  // Field exists
{ field: { $type: "string" } } // Field is of type


db.books.find({"title":{$type:10}})                     // find books that have null in the title
db.books.find({"price":{$type: 'string'}})                     // find books that their price is string
db.books.find({"price":{$type: 'number'}})                     // find books that their price is number
db.books.find({ price: { $type: ["int", "double"] } })         // match multiple types
db.books.find({"price":{$exists: true}})                     // find books that have "price" field 
db.books.find({"price":{$exists: false}})                     // find books that does not have "price" field 
```

##### 8.5 Anti-Pattern operators

+ operators:
    1. $nin
    2. $not
    3. $ne
    4. $exists: false

```js

```

##### 8.5 Evaluation Operators

+ `Evaluation operators` are used to match documents based on expressions like
    * text searches
    * regex patterns
    * JavaScript evaluation
    * math evaluation with arithmetic operations

+ operators:
    1. $expr        
        * syntax: `{$expr:{ $operator: [field,value] }}`
        * Use aggregation expressions in find() operations for comparison
        * Compare values between fields in the same document
    2. $jsonSchema
    3. $mod
        * `$mod: [ divisor, remainder ]`
    5. $regex
    6. $where ---> js function
    7. $text


```js
db.books.find({$expr:{$gt:"$number",400}})                        // equivalent to {"number" : {$gt:400} } 
db.sales.find({$expr: { $gt: ["$spent", "$budget"] }})            // Matches docs where spent > budget   (compare fields of same doc)
db.orders.find({$expr: {$ne: ["$total",{ $sum: "$items.price" }]}})     // docs total doesn't match the sum of items (Using Aggregation Operators)
db.books.find({$expr: {$lt: [{ $multiply: ["$price", 2] },10]}})     // price * 2 < 10   // use arithmetic aggregation operations
db.products.find({quantity: { $mod: [4, 0] }})                      // Matches docs where quantity is divisible by 4
db.books.find(  {  $expr: { $lt: ["$price", 5]}, price: {$exists: false} } )   // multiple condition in addition to $expr
db.customers.find({name: { $regex: /^A/, $options: "i" }})            // Names starting with "A", case-insensitive
db.articles.find({$text: { $search: "mongodb lab" }})               // Matches docs containing "mongodb" or "lab" (full-text search on text index)
db.users.find({$where: "this.age > 25 && this.age < 40"})           // Matches docs where age is between 25 and 40 (less efficient)
db.users.find({                                                     // find docs that have name & email, and age >= 18
  $jsonSchema: {                                                    // multiple condition with jsonSchema
    bsonType: "object",
    required: ["name", "email"],                                 // required like $exists
    properties: {                                                 // each property like $type
      email: { bsonType: "string" },
      age: { bsonType: "int", minimum: 18 }
    }
  }
})
```



##### 8.8 cursor methods

```py
db.books.find({query}).count()
db.books.find({}).count()                          # number of docs in total
db.books.find({query}).sort({"title":-1})           # ordered bt
db.books.find({query}).limit(10)                   # return just 10 docs
db.books.find({query}).skip(5)                   # skip the first number of docs
db.books.find({query}).size()                   # size is similar to count() but it used after skip() or limit()
```


##### 8.9 expr vs normal filter

```js
// both are the same but                         // both just check numeric values and not check string values
db.books.find({ $expr: { $lt: ["$beds", 4]}})   // $expr is more flexible and return docs that does not have beds
db.books.find({ beds: { $lt: 4 } })             // have {beds: { $exists: true}}  in the background
```



#### Aggregation Framework

+ MQL is sufficient for simple queries but aggregation Framework allows us for advanced operations
+ aggregation uses pipelines for operations
+ we use aggregation pipelines, which process documents step-by-step through stages
+ we can use each operations like $match more than once in pipelines
+ aggregation is similar to `GROUP BY` and aggregate functions in SQL, but much more flexible.
+ aggregation is the process of 
    * transforming doc shapes
    * filtering and sorting
    * grouping and counting docs
    * analyzing documents and calculating totals, averages, max/min, etc
    * produce computed results 
    * Performing joins between collections ($lookup)
+ syntax: `db.books.aggregate([{stage1},{stage2},{stage3}],{options})`

+ Pipeline Stages:
    1. $match:             Filters documents (like WHERE in SQL)
    2. $group              Groups documents by key
    3. $project            Reshapes documents (selects fields)
    4. $addFields          Adds new fields without removing existing ones
    5. $sort               Orders documents
    6. $limit, $skip       Pagination
    7. $lookup             Joins collections (like LEFT JOIN)
    8. $unwind             Deconstructs arrays
    9. $bucket, $bucketAuto Grouping docs into rages
    10. $facet             Runs multiple pipelines in parallel in a single stage
    11. $count             	Count documents in the pipeline


##### 1. $match stage

+ `$match` stage takes one argument
+ if you want to use expression operators in `$match` stage, use `$expr`

```js
db.books.aggregate([{$match: {'year':{$gte:2005,$lte:2004}}}])          // filter docs
db.books.aggregate([{$match:{'year':2005}}])                          //  db.books.find({"year":2005})
db.books.aggregate([{ $match: {price: {$type: 'number'}, $expr:{$gt:["$price", 400] }}}])     // takes multiple conditions in one argument
db.users.aggregate([                                                   // Find users created in 2023
  { $match: { 
    createdAt: { $gte: ISODate("2023-01-01"), $lt: ISODate("2024-01-01") }
  }}
])
```

##### 2. $project, $addFields

```js
db.books.aggregate([{$match: {'year':2005}}], {$project:{'year':1, 'title':1}})                  // projection like find() method
db.books.aggregate([{$project: {'price':1},{$match:{price:{$gt,100}}}])                       // we can also use $project at first
db.books.aggregate([{$project: {'number':1, '_id':0}}])                                      // Exclude _id (must explicitly exclude)
db.books.aggregate([{$project: {'number':1, 'new_field': '$number'}}])                          // field renaming
db.books.aggregate([{$project: {'price':1, 'price_toman': {$divide:['price',100]}}}])           // show new field based on existing field
db.books.aggregate([{$project: {'price':1, 'isPriceOver100':{$gt:['price',100]}}}])      // comparison operator returns True/Flase in new field
db.books.aggregate([{$project: {'price':1, 'isPriceOver100': 'price':{$gt:100}}])      // not work in this format
db.books.aggregate([{$project: {'number_of_tags':{$size:'tags'}}},{$match:{'number_of_tags':{$gt:20}}}])   // condition for new field

// addFields
db.books.aggregate([{$addFields: {'new_field': '$number'}}])               // unlike $projection this new field is not the only field           
db.trips.aggregate([{$addFields: {'duration_hrs':{$divide:['$duration',3600]}}}])           // Adding Computed Fields
db.trips.aggregate([{$addFields: {'topScore':{$max: '$scores'}}}])                          // array field information
```


##### 3. cursor stages

+ Cursor stages in MongoDB's aggregation pipeline allow you to control how results are returned and processed.
+ These stages are typically used at the end of an aggregation pipeline to manage output
+ cursor stages:
    1. $limit
    2. $skip
    3. $sort
    4. $count
    5. $out


```js
db.books.aggregate([{ $match: { status: "completed" } },{ $limit: 10 } ])              // just return 10 items
db.books.aggregate([{ $limit: 5 } ])                                                   // just return 5 items
db.books.aggregate([{ $skip: 5 } ])                                                    // skip the first 5 items
db.books.aggregate([{ $skip: 5 }, { $limit: 6 } ])                                   // return 6 items after skipping the first 5
db.books.aggregate([{ $limit: 6 }, { $skip: 5 } ])                                  // return 6th item (just one doc)
db.books.aggregate([{ $sort: { price: -1 } } ])
db.books.aggregate([{ $match: { region: "EMEA" } },{ $count: "count_field_name" } ])
```

##### 4. $group stage

+ $group in aggregation groups documents by specified identifiers and applies accumulator expressions to each group.
+ $group is something like merging or summarizing with statistics
+ $sort is often useful to sort grouped results
+ _id in $group stage is diffrent from id in docs.
+ return  just _id and fields that we specify in $group stage
+ syntax: 
    `db.collection.aggregate([{$group: {
        _id: <expression>,
        new_field1: { <accumulator1>: <expression1> },
        new_field2: { <accumulator2>: <expression2> },
    }}])`
+ key components:
    1. _id:  Mandatory, specifies the grouping key
    2. Accumulator operators: Perform calculations on grouped documents
        * $sum, $avg, $min, $max, $push, $first, $last, $addToSet

```js
db.books.aggregate([{$group: {_id: '$author'}}])                                    // return distinct authors
db.books.aggregate([{$group: {_id: '$author', new_field: {$sum: '$price'}}])         // sum price for distinc authors
db.books.aggregate([{$group: {_id: '$author', new_field: {$sum: '$price'}, {$max: '$price'}}])         // multiple accumulators
db.books.aggregate([{$group: {_id: '$author', new_field: {$sum: '$price'}}, {$sort:"$new_field": -1}])         // sort the values
db.books.aggregate([{$group: {_id: null, new_field: {$sum: '$price'} } }])       // group all docs and calculate sum price of all docs
db.books.aggregate([{$group: {_id: {'$author', '$ISBN'}, total:{$sum:'$number'}}}])          // group by multiple values
db.orders.aggregate([{$group: {_id: '$customerId', listOfPurchased: {$psuh: '$product'}}}])  // Group customers with their purchased items
```

#### $bucket and $bucketAuto

+ grouping docs into ranges, we specify rages by an array
+ [0, 100, 200] creates 2 rangrs: 1. 0-99 2. 100-199   (lower bound in inclusice and upper bound is exclusive)
+ $bucket manually define ranges
+ $bucketAuto we define number of groups and mongo decides the bucket boundaries
+ syntax: `{$bucket: { groupBy: '$field', boundaries: [], default: '', output: { new_filed: { <$accumulator1> : <expression1> }, ...} }}`
+ syntax: `{$bucketAuto: { groupBy: '$field', bucket: 4, output: {<outputField1>: { <accumulator> : <expression> }} }}`
    + `group by` and `boundaries` are required and others are optional
    + `default` is a label for values that don’t fall into any bucket
    + if we do not specify `output` by default the accumulator is $count and retunred field_name is 'count'

```js
db.books.aggregate([{$bucket: {groupBy: '$number', boundaries: [0, 100, 200, 300]}}])         // by default return 2 item (range, accumulator)
db.books.aggregate([{$bucket: {groupBy: '$number', boundaries: [0, 100, 200, 300, "a"]}}])       // we get error with 'a' they should be same type
// without default we get error for values outside of boundry
db.books.aggregate([{$bucket: {groupBy: '$number', boundaries: [0, 100, 200, 300],default: 'other'}}]) 
// we can specify multiple accumulators
db.books.aggregate([{$bucket: {groupBy: '$number', boundaries: [0, 100, 200, 300],output: { new_filed:{$avg:'$number'}}}}}}])  
db.books.aggregate([{$bucketAuto: { groupBy: '$number', bucket: 5}}])                       // count into 5 groups
```


##### 9. $facet stage

+ $facet process multiple aggregation pipelines within a single stage.
+ these sub-pipelines work independetly in parallel which means different pipelines operate on the same set of input documents (not data of prevouos satge)
+  useful for generating a single output that contains the results of various analyses all at once
+ $facet returns all results together in a single document.
+ syntax: `{$facet: new_field1: [ <stage1>, <stage2>, ... ], new_field2: [<stage1>, <stage2> ,...] }}`

```js
db.products.aggregate([
  {
    $facet: {
      "totalProducts": [
        { $count: "count" }
      ],
      "averagePrice": [
        { $group: { _id: null, avgPrice: { $avg: "$price" } } }
      ],
      "productsByCategory": [
        { $group: { _id: "$category", products: { $push: "$$ROOT" } } }
      ]
    }
  }
])
db.products.aggregate([
  {
    $facet: {
      priceBuckets: [
        { $bucket: {
            groupBy: "$price",
            boundaries: [0, 50, 100, 200, 500],
            default: "Other",
            output: { count: { $sum: 1 } }
        } }
      ],
      topExpensive: [
        { $sort: { price: -1 } },
        { $limit: 5 },
        { $project: { name: 1, price: 1 } }
      ]
    }
  }
])
```


##### 8. unwind

+ $unwind is used to deconstruct an array field, creating a separate document for each element in the array.
+ This is particularly useful after a $lookup operation or when working with array fields in general.
+ syntax1: `{$unwind: $array_field}`
+ syntax2: `{$unwind: { path: '$array_field', includeArrayIndex: "", preserveNullAndEmptyArrays: false } }`
    + includeArrayIndex (optional): Adds a field containing the index of the element
    + preserveNullAndEmptyArrays (optional)

```js
// Original document: { _id: 1, items: ["A", "B", "C"] }
db.inventory.aggregate([{ $unwind: "$items" }])    
// Results in:
// { _id: 1, items: "A" }
// { _id: 1, items: "B" }
// { _id: 1, items: "C" }

db.inventory.aggregate([
  { 
    $unwind: {
      path: "$items",
      preserveNullAndEmptyArrays: true // Keep docs even if array is empty/null
    }
  }
])

// Convert joined array to individual documents
db.orders.aggregate([
  {
    $lookup: {
      from: "products",
      localField: "productId",
      foreignField: "_id",
      as: "productDetails"
    }
  },
  { $unwind: "$productDetails" } // Convert array to object
])
```


##### 5. $lookup

+ The $lookup stage performs a left outer join between documents
+ the matching docs are returned as an array of embeded documents (name of array comes from "as")

```js
db.authors.aggregate([               // Join authors with books
  {
    $lookup: {
      from: "books",
      localField: "_id",
      foreignField: "authorId",
      as: "publishedBooks"
    }
  }
])
db.authors.aggregate([{$lookup}, {$lookup}])      // multiple lookups
```

###### 5.1 Pipeline $lookup Syntax

+ The pipeline syntax in $lookup provides much more flexible joining capabilities
+ we can use pipeline for Nested Lookups
+ key components:
    1. let:             Defines variables from the input documents
    2. pipeline:        Runs on the joined collection, can reference the variables

```js
db.orders.aggregate([
  {
    $lookup: {
      from: "inventory",
      let: { order_item: "$item", order_qty: "$quantity" },             // rename fields for using in this query
      pipeline: [                                                      // instead of localField we use pipeline
        {
          $match: {
            $expr: {
              $and: [
                { $eq: ["$sku", "$$order_item"] },
                { $gte: ["$instock", "$$order_qty"] }
              ]
            }
          }
        }
      ],
      as: "inventory_docs"
    }
  }
])

db.users.aggregate([                            // nested lookup
  {
    $lookup: {
      from: "posts",
      let: { userId: "$_id" },
      pipeline: [
        { $match: { $expr: { $eq: ["$author_id", "$$userId"] } } },
        {
          $lookup: {
            from: "comments",
            let: { postId: "$_id" },
            pipeline: [
              { $match: { $expr: { $eq: ["$post_id", "$$postId"] } } },
              { $count: "count" }
            ],
            as: "comment_count"
          }
        },
        { $unwind: "$comment_count" }
      ],
      as: "user_posts"
    }
  }
])
```


#### correlated subquesy ???
#### pre-aggregating data ???



#### Aggregation-Only Operators  

+ aggregation operators or expression operators --vs-- query operators
+ we cannot use this operators in find() method, they should be inside aggregate()
+ when you want to use expression operators in `$match` stage you should use `$expr`
+ operators that we can use in both find() and aggregate():
    - $gt, $lt, $gte, $lte, $eq, $ne
    - $in, $nin
    - $type, $exists
    - $mod
    - $regex


##### 1. arithmetic aggregation operators

+ we have 2 types of arithmetic operations:
    1. Arithmetic Update Operators          $inc, $mul, $max, $min
    2. Arithmetic Aggregation Operators

+ whenever you want to use arithmetic operator inside `find()` or `$match` state, we should use `$expr`
+ operators:
    + $add, $subtract, $multiply, $divide
    + $ceil, $floor, $round
    + $pow, $sqrt, $abs, $log
    + $max, $min, $avg      

```js
db.books.aggregate([{$project: "new_field": {$add: [$price, 100] }}])
db.books.aggregate([{$project: "new_field": {$round: [$price, 1] }}])         // round with one decimal place
db.books.aggregate([{$match: $expr: {$gt:[{$multiply: ["$price",3]}, "$buy_price"]} }])         // $match operator   price * 3 > buy_price
```

##### 2. string operators

+ operators:
    1. $concat
    2. $substrBytes
    3. $substrCP
    4. $toLower, $toUpper
    5. $strcasecmp                Case-insensitive string comparison
    6. $split                     Splits a string into an array
    7. $strLenCP                  Comparison two strings
    8. $trim, $ltrim, $rtrim
    9. $replaceOne, $replaceAll
    10. $regexMatch

```js
db.users.aggregate([{ $project: { fullName: { $concat: ["$firstName", " ", "$lastName"] } } }])
db.users.aggregate([{ $project: { subName: { $substrCP: ["$name", 0, 5] } } }])             // extract substring
db.users.aggregate([{ $project: { nameLower: { $toLower: "$name" } } }])                    // toLower or toUpper
db.users.aggregate([{ $project: { compare: { $strcasecmp: ["$name", "Ali"] } } }])         // comparison two strings  >0 → first > second
db.users.aggregate([{ $project: { parts: { $split: ["$email", "@"] } } }])                  // split
db.users.aggregate([{ $project: { cleaned: { $trim: { input: "$username", chars: " " } } } }])  // Removes spaces from both ends
```

##### 3. comparison operators

+ operators:
    + $eq, $ne, $gt, $lt, $gte, $lte
+ syntax in `$expr` : `{ $operator: [exp1, exp2] }`
+ syntax in find(): `{field: {$operator: value}}`
+ we can use both syntax in `$match` stage but we cannot use `$expr` syntax in `$project` stage

```js
db.books.aggregate([{$match: { $expr: {$gt: ["$price", 100]}} }])
db.books.aggregate([{$macth: { price:{ $gt: 100} }}])
db.books.aggregate([{$project: { price: { $gt: 100} }}])
```

##### 3. date operators


+ we can use date operators in two ways:
    1. using $expr
    2. using aggregation pipelines

+ date operators:
    1. date Extraction operators
    2. date Conversion operators
    3. date Creation operators
    4. date Arithmetic operators

###### 3.1 Date Extraction Operators

+ we can use these in `$expr` or in aggregation stages like `$project`
+ field should be ISODate
+ operators:
    1. $year, $month, $week
    2. $hour, $minute, $second, $millisecond
    3. $dayOfMonth, $dayOfWeek, $dayOfYear
    4. $isoWeek, $isoWeekYear, $isoDayOfWeek

```js
db.events.find({ $expr: { $eq: [ { $year: "$startDate" }, 2025 ] } })                // Matches docs where year(startDate) = 2025
db.events.find({ $expr: { $eq: [ { $month: "$startDate" }, 8 ] } })                // Matches docs where month(startDate) = August
db.events.find({ $expr: { $eq: [ { $dayOfMonth: "$startDate" }, 15 ] } })        // Matches docs where day of month = 15
db.events.find({ $expr: { $eq: [ { $dayOfWeek: "$startDate" }, 1 ] } })           // Matches docs where day of week = Sunday (1=Sun … 7=Sat)
db.events.find({ $expr: { $eq: [ { $hour: "$startDate" }, 9 ] } })                // Matches docs where hour(startDate) = 9
```


###### 3.2 Date Arithmetic Operators

+ "$$NOW" is a variable
+ operators:
    1. $dateAdd, $dateSubtract
    2. $add, $subtract
    3. $dateDiff

```js
// Matches docs where endDate is more than 7 days after startDate
db.events.find({ $expr: { $gt: [ "$endDate", { $dateAdd: { startDate: "$EvnetDate", unit: "day", amount: 7 } } ] } })
db.books.aggregate([{$project: { new_field: {$dateAdd :{ startDate: "$publish_date", unit: 'week', amount:4 } } } }])  // return ISODate
db.books.aggregate([{$project: { new_field: {$dateSubtract :{ startDate: "$publish_date", unit: 'month', amount:4 } } } }])  
db.books.aggregate([{$project: { new_field: {$dateDiff :{ startDate: "$publish_date", endDate: "$$NOW", unit: 'year' } } } }])  
```

###### 3.3 Date Conversion Operators

+ operators:
    1. $dateToString
    2. $dateToParts
    3. toDate

```js
db.books.aggregate([{$project: { new_field:  {$dateToString: {format: "%Y--+--%m" , date: "$publish_date" } }  } } ])
db.events.find({ $expr: { $eq: [ { $dateToString: { format: "%Y-%m-%d", date: "$startDate" } }, "2025-08-09" ] } })
// Matches docs where startDate formatted as YYYY-MM-DD equals "2025-08-09"

db.events.find({ $expr: { $eq: [ { $toDate: "$timestampStr" }, ISODate("2025-08-09T00:00:00Z") ] } })
// toDate() converts string field to ISODate               // timestamp like 2007-03-28-14.50.35.123
```

###### 3.4 Date Creation Operators

+ operators:
    1. $dateFromParts
    2. $dateFromString

```js
// Matches docs where startDate equals 2025-08-09
db.events.find({ $expr: { $eq: [ "$startDate", { $dateFromParts: { year: 2025, month: 8, day: 9 } } ] } })

// Matches docs where startDate equals parsed date from string
db.events.find({ $expr: { $eq: [ "$startDate", { $dateFromString: { dateString: "2025-08-09" } } ] } })
```


##### 4. array operators

+ operators:
    1. $isArray
    2. $arrayElemAt, $indexOfArray
    3. $first, $last
    4. $size
    5. $concatArrays, $slice
    6. $arrayToObject, $objectToArray
    7. $map, $filter, $reduce
        + `{$map: {input: array, as: "item", in: {expr}}}`
        + `{$filter: {input: array, as: "item", cond: {expr}}}`
        + `{$reduce: {input: array, initialValue: initialValue, in: {expr}}}`

+ set operators:
    1. $setDifference
    2. $setIntersection
    3. $setUnion
    4. $setIsSubset
    5. $setEquals


```js
db.books.aggregate([{$project: { new_field: { $arrayElemAt: [ "$authors_array", 0 ] } } }])
{ $indexOfArray: [["A", "B", "C"], "B"] }                            // Returns 1
{ $arrayElemAt: [["A", "B", "C"], 1] }                               // Returns "B"
{ $in: ["B", ["A", "B", "C"]] }                                   // Returns true
{ $first: ["A", "B", "C"] }                                       // Returns "A"
{ $last: ["A", "B", "C"] }                                          // Returns "C"
{ $size: ["A", "B", "C"] }                                         // Returns 3
{ $sortArray: { input: ["A", "B", "C"], sortBy: 1 }}                     // 1 for ascending, -1 for descending
{ $reverseArray: ["A", "B", "C"] }                                 // reverses the array
{ $setDifference: [["A", "B"], ["B", "C"]] }               // Returns ["A"]        // Returns elements in first set that aren't in second set
{ $setIntersection: [["A", "B"], ["B", "C"]] }             // Returns ["B"]        // Returns elements that are in both sets
{ $setUnion: [["A", "B"], ["B", "C"]] }                   // Returns ["A", "B", "C"]
{ $slice: [["A", "B", "C", "D"], 1, 2] }                     // Returns ["B", "C"]
{ $concatArrays: [["A", "B"], ["C", "D"]] }                 // Returns ["A", "B", "C", "D"]
{ $range: [0, 5, 2] }                                // Returns [0, 2, 4]  // generates numbers from 0 to 5, incrementing by 2 like python
{ $zip: { inputs: [["A", "B"], [1, 2]] } }                // Returns [["A", 1], ["B", 2]]    // merges two arrays
// 1. $filter
{
  $filter: {
    input: [1, 2, 3, 4],
    as: "num",
    cond: { $gt: ["$$num", 2] }
  }
}              // Returns [3, 4]
// 2. $reduce
{
  $reduce: {
    input: [1, 2, 3],
    initialValue: 10,
    in: { $add: ["$$value", "$$this"] }
  }
}          // Returns 16 (10 + 1 + 2 + 3)
// 3. $map
{
  $map: {
    input: [1, 2, 3],
    as: "num",
    in: { $multiply: ["$$num", 2] }
  }
}              // Returns [2, 4, 6]
// Calculating Array Statistics
db.students.aggregate([
  {
    $project: {
      averageScore: { $avg: "$scores" },
      topScore: { $max: "$scores" },
      scoreCount: { $size: "$scores" }
    }
  }
])
// Filtering and Transforming Arrays
db.orders.aggregate([
  {
    $project: {
      highValueItems: {
        $filter: {
          input: "$items",
          as: "item",
          cond: { $gt: ["$$item.price", 100] }
        }
      }
    }
  }
])
```



##### 5. conditional operators

+ operators:
    1. $cond (if-then-else)
        + return something if the condition is true and something else if the condition is false
        + ternary syntax: `{ $cond: { if: {}, then: <true-case>, else: <false-case> } }`
        + array syntax : `{ $cond: [ {}, <true-case>, <false-case> ] }`
    3. $ifNull
        + return something if the value is null
    4. $switch

```js
// 1. $cond
db.students.aggregate([{$project: {
    new_field: {
        $cond: {
            if: { $gte: ["$score", 60] },
            then: "you pass",
            else: "you fail"
        }
    }
}}])
// 2. $ifNull            // description is a field and we want to return something else for docs that have null description
db.products.aggregate([{$project: {description: { $ifNull: ["$description", "No description available"] }}}])
// 3. $switch
db.students.aggregate([{$project: {
    new_field: {
        $switch: {
          branches: [                                                    // branches is part of syntax
            { case: { $gte: ["$score", 90] }, then: "A" },
            { case: { $gte: ["$score", 80] }, then: "B" },
            { case: { $gte: ["$score", 70] }, then: "C" },
            { case: { $gte: ["$score", 60] }, then: "D" }
          ],
          default: "F"
        }
    }
}}])
```




##### 6. Type Conversion Operators

+ operators:
    1. $convert
        + onError: 
        + onNull:
    2. $toInt, $toLong, $toDecimal, $toDouble, $toString, $toDate

+ type checking operators:
    + $isNumber, $isString, $isBoolean, $isDate, $isObjectId

```js
// find docs that price is greater than 100      // price field is mixed of numbers and strings
db.collection.find({
  $expr: {
    $gt: [
      { $convert: { input: "$price", to: "double", onError: 0, onNull: 0 } },
      100
    ]
  }
})
// Converting String Numbers for Calculation
db.products.aggregate([{$project: {
    new_field: {
        $multiply: [
          { $toInt: "$quantityStr" },
          { $toDecimal: "$priceStr" }
        ]
    }
}}])
//  we can use `$switch` for handeling mixed types
{$switch: {
    branches: [
        { case: { $isNumber: "$amount" }, then: "$amount" },
        { case: { $isString: "$amount" }, then: { $toDecimal: "$amount" } }
      ],
      default: 0
    }
}
```

### variables

##### 1. system varibals

+ use two $$ for system variables
+ system varibals are upper case
| Variable         | Meaning                                                  |
| ---------------- | -------------------------------------------------------- |
| `$$CURRENT`      | The current document at the current stage.               |
| `$$ROOT`         | The root document at the start of the stage.             |
| `$$REMOVE`       | Removes a field when used in `$project` or `$addFields`. |
| `$$NOW`          | Current datetime (UTC).                                  |
| `$$CLUSTER_TIME` | Cluster time for the operation.                          |

```js
// push every book in an array        // books_array shows all the books belongs to the same tags
db.books.aggregate([{$group:{'_id': '$tags', 'books_array':{$push:'$ROOT'}}}])             // common example
db.books.aggregate([{$project: {"$$NOW":1, $$CLUSTER_TIME: 1}}])
db.users.aggregate([{ $project: { originalDoc: "$$ROOT" } }])
```

##### 2. shell variables

+ The MongoDB Shell is based on JavaScript, so you can declare and use variables like in JS.

```js
var name= "Ali"
let age = 30;
const city = "New York";

db.users.find({ name: name, age: { $gt: age } });
print(`Name: ${name}, Age: ${age}, City: ${city}`);
```

##### 3. user defined varibals with $let

+ syntax: `{$let: {vars: { a: "a", b: "b" }, in: {}  }}`
+ Defines temporary variables usable only inside the `in` expression.
+ use $$ for your variables

```js
db.books.aggregate([{$project:{result :{ $let:{ vars:{a:50, b:100}, in:{$sum:["$$a","$$b"]} } }   } }])    // result: 150
db.sales.aggregate([
  {
    $project: {
      totalWithTax: {
        $let: {                                                   // return a computed value inside "in"
          vars: {                                                   // define your variables here
            total: { $multiply: ["$price", "$quantity"] },             // computes price × quantity
            taxRate: 0.08
          },
          in: { $add: ["$$total", "$$taxRate"] }                // the only place we can use our variables
        }
      }
    }
  }
])
```




### limit and thresholds

+ the max size of BSON document is 16MB
+ Maximum Nested Depth is 100 levels
+ Maximum Stages in Aggregation	1000
+ Maximum Indexes per Collection is 64
+ Max length for database.collection names is 123 bytes
+ each individual stage has a limit of 100MB of RAM
+ Aggregation pipeline stages execute entirely in memory

```js
db.collection.find({}).sort({}).allowDiskUse()
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$product", total: { $sum: "$price" } } },
  { $sort: { total: -1 } }
], { allowDiskUse: true })  // Bypasses 100MB RAM limit
```





### Index

+ Indexes are data structures (a sorted list) that improve query performance  just like an index in a book
+ indexes allows db to locate docs without scanning entire collections.
+ mongodb use b-tree for scaning the index list
+ too many indexes slows down writes
+ index takes up storage space
+ if you create an index, you do not need to sort() anymore
1. Without Index:   MongoDB scans every document (called a "collection scan") to find matches (slow)
2. With Index:      MongoDB uses a sorted list of values to jump directly to matching documents (fast)

```js
// when you create an index it returns the name of index                // when you want to delete an index, you need this name
db.users.createIndex({"email":1})    // Create an index on the "email" field  // 1 is ascending -1 is decending
// db.users.find({ email: "user@example.com" });         // Now this query is much faster
db.users.createIndex({"email":1, "first_name" : -1})    // index on multiple fields  (order is important)
db.users.createIndex({"author.first_name":1})                  // Create an index on object or array of object
db.articles.createIndex({ title: "text", content: "text" })    // Enable text search on fields
db.users.createIndex({ email: 1 }, { unique: true })           // Only unique fields can include indexes
db.users.createIndex({ email: 1 },{ partialFilterExpression: { status: "active" } })       // Index only documents matching filter
db.users.createIndex({ email: 1 },{ partialFilterExpression: { number: {$gt: 12} } })       // Index only documents matching filter
db.logs.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })                     // Auto-delete documents after time period
db.users.createIndex({"email":1}, {name: "ix_email"})    // Custome name
db.users.createIndex({"email":1}, { background: true})    // Builds index without blocking operations  (by default locks read and write)
```



##### Managing Indexes

+ suppose we have multiple indexes for a field: 1. simple index 2. index with partialFilterExpression
+ we can use `hint()` to specify and choosing one of the the indexes

```js
db.collection.getIndexes()              // View Existing Indexes
db.collection.dropIndex("index_name")    // remove
db.collection.dropIndex({ field: 1 })   // remove
db.collection.find({ email: "test@example.com" }).hint("name_of_index")      // Force Index Usage
```

##### explain() method

+ The explain() method provides detailed information about how queries and operations are executed
+ helping you analyze and optimize performance
+ Explain Verbosity Modes:
    1. queryPlanner (default):            Shows winning plan
    2. executionStats:                    Includes execution statistics
    3. allPlansExecution:                 Shows stats for all considered plans (rejected and succeed)

```js
db.users.find({}).explain()                             // same as db.users.find({}).explain("queryPlanner")
db.users.find({}).explain("executionStats")
db.users.find({}).explain("allPlansExecution")

// output
db.orders.find({status: "completed",total: { $gt: 100 }}).explain("executionStats")
{
    ///////// 1. Verbosity mode: queryPlanner
    "queryPlanner": {                      // Basic query information.        // queryPlanner have 2 fields: 1. winningPlan 2. rejectedPlans
        "plannerVersion": 1,               // Version of the query planner
        "namespace": "shop.orders",        // Database.Collection being queried
        "indexFilterSet": false,           // Whether index filters were applied
        "parsedQuery": {                   // How MongoDB interpreted your query
          "$and": [                         // for queryPlanner the query will not execute
            { "status": { "$eq": "completed" } },
            { "total": { "$gt": 100 } }
          ]
        },

        "winningPlan": {                       // The selected query execution plan
          "stage": "FETCH",                // Retrieves full documents
          "inputStage": {                  // Child operation that feeds documents
            "stage": "IXSCAN",            // Index scan (good - using index)        // values: 1. "COLLSCAN" (collection scan) 2. IXSCAN
            "keyPattern": {
              "status": 1,                 // Index was on status field
              "total": 1                   // and total field (compound index)
            },
            "indexName": "status_1_total_1", // Name of the index being used
            "isMultiKey": false,           // Not an array/multikey index
            "direction": "forward",        // Index traversal direction    // values: 1. forward (sort:1) 2. backward (sort:-1)
            "indexBounds": {               // How the index is being searched
              "status": [
                "[\"completed\", \"completed\"]"  // Exact match
              ],
              "total": [
                "(100.0, inf.0]"          // Range greater than 100
              ]
            }
          }
        },

        "rejectedPlans": [         // Alternative plans MongoDB considered but rejected
          {
            "stage": "FETCH",
            "filter": { "total": { "$gt": 100 } }, // Additional filtering
            "inputStage": {
              "stage": "IXSCAN",          // Another possible index
              "keyPattern": { "status": 1 },
              "indexName": "status_1",
              // ... similar index details ...
            }
          }
        ]
    },

    ///////// 2. Verbosity mode: executionStats
    "executionStats": {                 // Detailed execution statistics in addition to queryPlanner if you specify Verbosity mode
        "executionSuccess": true,         // Did the query succeed? for this Verbosity mode the query will execute
        "nReturned": 42,                  // Number of documents returned
        "executionTimeMillis": 3,        // Total execution time (ms)
        "totalKeysExamined": 42,          // Number of index entries scanned
        "totalDocsExamined": 42,          // Number of documents examined
        "executionStages": {               // Breakdown of execution stages
          "stage": "FETCH",               // Document retrieval stage
          "nReturned": 42,
          "docsExamined": 42,
          "inputStage": {                 // Child stage (index scan)
            "stage": "IXSCAN",
            "nReturned": 42,
            "keysExamined": 42,
            "indexName": "status_1_total_1",
            // Time spent in this stage
            "executionTimeMillisEstimate": 1
          },
          "executionTimeMillisEstimate": 2
        },
       ///////// 3. Verbosity mode: allPlansExecution 
        // Contains statistics in 2 object for all considered plans  (wining and rejected)
        "allPlansExecution": [                         // you see this array inside executionStats if you specify Verbosity to allPlansExecution
          {
            "nReturned": 42,
            "executionTimeMillisEstimate": 3,
            // ... similar stats for winning plan ...
          },
          {
            "nReturned": 120,             // More documents examined
            "executionTimeMillisEstimate": 8,
            // ... stats for rejected plan ...
          }
        ]
    },

    "serverInfo": {                      // Server information you get for all modes of Verbosity
        "host": "db-server-1",           // Server that executed the query
        "port": 27017,
        "version": "5.0.6",              // MongoDB version
        "gitVersion": "..."              // Build details
    },
    "ok": 1                            // Success flag
}
```






