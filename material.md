# 🍃 MongoDB NoSQL – Complete Guide
A comprehensive guide for learning MongoDB, a popular NoSQL database for modern applications.

---

## 📋 Table of Contents

- [What is MongoDB](#-what-is-mongodb)
- [Installation](#-installation)
- [Running MongoDB](#-running-mongodb)
- [MongoDB Clients](#-mongodb-clients)
- [Database Basics](#-database-basics)
- [Collections](#-collections)
- [Data Modeling](#-data-modeling)
- [BSON Data Types](#-bson-data-types)
- [Insert Documents](#-insert-documents)
- [Query Documents](#-query-documents)
- [Query Operators](#-query-operators)
- [Update Documents](#-update-documents)
- [Delete Documents](#-delete-documents)
- [Bulk Operations](#-bulk-operations)
- [Indexes](#-indexes)
- [Security](#-security)
- [User Management](#-user-management)
- [Backup & Restore](#-backup--restore)

---

## 🎯 What is MongoDB

MongoDB is a free and open-source database management system with the following characteristics:

- **Document-based database** - Stores data in flexible, JSON-like documents
- **Developed by 10gen** - Created in 2007, publicly released in 2009
- **Now MongoDB Inc** - Company renamed from 10gen to MongoDB Inc
- **Multi-language support** - Supports nearly all programming languages as clients
- **JavaScript-based** - Uses JavaScript instead of SQL for document manipulation

---

## 📦 Installation

- **Download MongoDB** - Download MongoDB as an archive file at  `https://www.mongodb.com/try/download/community`
- **Extract the archive** - Extract the downloaded file to your computer
- **Locate the binary** - Inside the archive, you'll find `bin/mongod` (MongoDB daemon) to run the MongoDB Server

---

## ▶️ Running MongoDB

- **Create data directory**
   
   ```bash
   mkdir -p /path/to/data
   ```

- **Start MongoDB Server**
   
   ```bash
   bin/mongod --dbpath=/path/to/data
   ```

- **Stop MongoDB Server**
   
   Press `Ctrl + C`

---

## 💻 MongoDB Clients

Unlike other databases (MySQL/PostgreSQL), MongoDB only runs as a server application. To connect to MongoDB, you need a client application.



-  **MongoDB Compass**

    - Free GUI-based MongoDB client that provides a graphical interface and allows executing commands directly.
    
    - Download at `https://www.mongodb.com/products/tools/compass`
    
    - Default Connection MongoDB runs on port `27017`


- **MongoDB Shell**

    - Terminal-based client designed for Linux servers or systems without a GUI and for performing command-line operations.
    
    - Download at `https://www.mongodb.com/products/tools/shell`
    
    - `Usage:`
    
      ```bash
      # Extract the downloaded archive
      # Run mongosh from the bin directory
      bin/mongosh mongodb://host:port/database_name
      ```

---

## 🗄️ Database Basics

A database is a storage location for collections that provides logical separation, usually one database per application, and is created automatically by MongoDB.

- **Creating/Selecting Database**

  ```javascript
  // Switch to database (creates if doesn't exist)
  use study
  
  // Show all databases
  show databases
  ```

- **Database Methods**

  | Method | Description |
  |--------|-------------|
  | `db.dropDatabase()` | Delete database |
  | `db.getName()` | Get database name |
  | `db.hostInfo()` | Get host information |
  | `db.version()` | Get database version |
  | `db.stats()` | Get database statistics |

---

## 📚 Collections

Collections store documents with a maximum size of 16MB per document and a maximum nesting level of 100.

- **Collection Methods (Database Level)**

  | Method | Description |
  |--------|-------------|
  | `db.getCollectionNames()` | Get all collection names |
  | `db.createCollection(name)` | Create new collection |
  | `db.getCollection(name)` | Get collection object |
  | `db.<name>` | Same as `db.getCollection(<name>)` |
  | `db.getCollectionInfos()` | Get information about all collections |


- **Collection Methods (Collection Level)**

  | Method | Description |
  |--------|-------------|
  | `db.<collection>.find()` | Get all documents |
  | `db.<collection>.count()` | Get document count |
  | `db.<collection>.drop()` | Delete collection |
  | `db.<collection>.totalSize()` | Get total collection size |
  | `db.<collection>.stats()` | Get collection statistics |

---

## 🏗️ Data Modeling

- **Schema Flexibility**

  - **No predefined schema required** - Insert data directly without defining structure
  - **Flexible documents** - Each document in a collection can have different fields
  - **Best practice** - Use consistent data types across documents in the same collection

- **Primary Key (_id)**

  - **Mandatory field** - Every document must have an `_id` field
  - **Single field only** - Cannot create composite primary keys
  - **Auto-generated** - MongoDB creates ObjectId if `_id` is not provided

- **Embedded vs Reference**

  - `Embedded Sub-Document Example`
  
    ```json
    {
      "_id": "1",
      "name": "Dzaru Rizky Fathan Fortuna",
      "address": {
        "city": "Jakarta",
        "country": "Indonesia"
      },
      "contact": {
        "email": "dzarurizkybusiness@gmail.com",
        "phone": "081217147620"
      },
      "hobbies": ["Coding", "Gaming", "Reading"]
    }
    ```

  - `Document Reference Example`
  
    ```json
    // user.json
    {
      "_id": "dzaru",
      "name": "Dzaru Rizky Fathan Fortuna"
    }
    
    // address.json
    {
      "_id": "21312321",
      "user_id": "dzaru",
      "city": "Jakarta",
      "country": "Indonesia"
    }
    
    // contact.json
    {
      "_id": "2343434",
      "user_id": "dzaru",
      "email": "dzarurizkybusiness@gmail.com",
      "phone": "081217147620"
    }
    ```

- **When to Use Embedded**

  - Documents are interdependent
  - Cannot directly modify embedded documents
  - Embedded documents are always needed when retrieving data

- **When to Use Reference**

  - Documents can stand alone
  - Can directly manipulate reference documents
  - Reference documents are not always needed when retrieving data

---

## 📊 BSON Data Types

BSON (Binary JSON) is a binary-encoded serialization format for JSON-like documents.

<br/>

| Type | BSON Type |
|------|-----------|
| Double | `double` |
| String | `string` |
| Object | `object` |
| Array | `array` |
| Binary Data | `binData` |
| ObjectId | `objectId` |
| Boolean | `bool` |
| Date | `date` |
| Null | `null` |
| Regular Expression | `regex` |
| JavaScript | `javascript` |
| JavaScript with Scope | `javascriptWithScope` |
| 32 Bit Integer | `int` |
| Timestamp | `timestamp` |
| 64 Bit Integer | `long` |
| Decimal 128 | `decimal` |
| Min Key | `minKey` |
| Max Key | `maxKey` |

#### ObjectId

  - **Random and unique** data
  - **Fast generation** and ordered
  - **12-byte value**: 4 bytes timestamp + 5 bytes random value + 3 bytes incrementing counter
  - **Default _id**: Used as primary key if not explicitly specified

#### Date and ISODate

  - **BSON Date**: 64-bit integer representing milliseconds since Unix epoch (January 1, 1970)
  - **ISODate**: Time representation used by MongoDB
  - **JavaScript compatible**: Compatible with JavaScript Date

---

## ➕ Insert Documents

- **Insert Functions**

  | Function | Description |
  |----------|-------------|
  | `db.<collection>.insertOne(document)` | Insert single document |
  | `db.<collection>.insertMany(array<document>)` | Insert multiple documents |

- **Examples**

  `Insert Single Document`
  
    ```javascript
    db.customers.insertOne({
      _id: "dzaru",
      name: "Dzaru Rizky Fathan Fortuna"
    })
    ```

  `Insert Multiple Documents`
  
    ```javascript
    db.products.insertMany([
      {
        _id: 1,
        name: "Indomie Ayam Bawang",
        price: new NumberLong("2000")
      },
      {
        _id: 2,
        name: "Mie Sedap Soto",
        price: new NumberLong("2000")
      }
    ])
    ```

  `Insert with Embedded Documents`
  
    ```javascript
    db.orders.insertOne({
      _id: new ObjectId(),
      total: new NumberLong("8000"),
      items: [
        {
          product_id: 1,
          price: new NumberLong("2000"),
          quantity: new NumberInt("2")
        },
        {
          product_id: 2,
          price: new NumberLong("2000"),
          quantity: new NumberInt("2")
        }
      ]
    })
    ```
---

## 🔍 Query Documents

`Example:`

```javascript
// Find by _id
db.customers.find({ _id: "dzaru" })

// Find by name
db.customers.find({ name: "Dzaru Rizky Fathan Fortuna" })

// Find by price
db.products.find({ price: 2000 })

// Find by nested field
db.orders.find({ "items.product_id": 1 })
```

---

## 🔧 Query Operators

- ### Comparison Operators

  `Cheatsheet:`

  | Operator | Description |
  |----------|-------------|
  | `$eq` | Equals |
  | `$gt` | Greater than |
  | `$gte` | Greater than or equal |
  | `$lt` | Less than |
  | `$lte` | Less than or equal |
  | `$in` | Matches any value in array |
  | `$nin` | Matches none of the values in array |
  | `$ne` | Not equal |

  `Examples:`
  
  ```javascript
  // Equal
  db.customers.find({
    _id: { $eq: "dzaru" }
  })
  
  // Greater than
  db.products.find({
    price: { $gt: 3000 }
  })
  
  // In array
  db.products.find({
    category: { $in: ["handphone", "laptop"] },
    price: { $gt: 5000000 }
  })
  ```

- ### Logical Operators

  `Cheatsheet:`

  | Operator | Description |
  |----------|-------------|
  | `$and` | Joins queries with logical AND |
  | `$or` | Joins queries with logical OR |
  | `$nor` | Joins queries with logical NOR |
  | `$not` | Inverts query expression |


  `Examples:`

  ```javascript
  // AND operator
  db.products.find({
    $and: [
      { 
        category: { $in: ["handphone", "laptop"] },
        price: { $gt: 2000000 }
      }
    ]
  })
  
  // NOT operator
  db.products.find({
    category: {
      $not: { $in: ["handphone", "laptop"] }
    }
  })
  ```

- ### Element Operators

  `Cheatsheet:`

  | Operator | Description |
  |----------|-------------|
  | `$exists` | Matches documents with specified field |
  | `$type` | Matches documents with specified field type |

  `Examples:`
  
  ```javascript
  // Field exists
  db.products.find({
    category: { $exists: false }
  })
  
  // Field type
  db.products.find({
    category: { $type: "string" }
  })
  
  db.products.find({
    price: { $type: ["int", "long"] }
  })
  ```

- ### Evaluation Operators

  `Cheatsheet:`
  
  | Operator | Description |
  |----------|-------------|
  | `$expr` | Use aggregation expressions |
  | `$jsonSchema` | Validate documents against JSON schema |
  | `$mod` | Perform modulo operation |
  | `$regex` | Match with regular expression |
  | `$text` | Perform text search |
  | `$where` | Match with JavaScript function |

  `Examples:`

  ```javascript
  // Expression operator
  db.customers.find({
    $expr: { $eq: ["$_id", "$name"] }
  })
  
  // JSON Schema operator
  db.products.find({
    $jsonSchema: {
      required: ["name"],
      properties: {
        name: { type: "string" },
        price: { type: "number" }
      }
    }
  })
  
  // Modulo operator
  db.products.find({
    price: { $mod: [1000000, 0] }
  })
  
  // Regex operator
  db.products.find({
    name: {
      $regex: /mie/,
      $options: "i"
    }
  })
  
  // Where operator
  db.customers.find({
    $where: function() {
      return this._id === this.name
    }
  })
  ```

- ### Array Operators

  `Cheatsheet:`
  
  | Operator | Description |
  |----------|-------------|
  | `$all` | Matches arrays containing all specified elements |
  | `$elemMatch` | Matches if array element meets conditions |
  | `$size` | Matches arrays with specified size |

  `Examples:`

  ```javascript
  // All operator
  db.products.find({
    tags: { $all: ["samsung", "monitor"] }
  })
  
  // Element match
  db.products.find({
    tags: {
      $elemMatch: { $in: ["samsung", "logitech"] }
    }
  })
  
  // Size operator
  db.products.find({
    tags: { $size: 3 }
  })
  ```

- ### Projection Operators

  Projection allows selecting specific fields to return.

  `Cheatsheet:`
  
  | Operator | Description |
  |----------|-------------|
  | `$` | Limit array to first matching element |
  | `$elemMatch` | Limit array to first element matching condition |
  | `$meta` | Return metadata from matching documents |
  | `$slice` | Control number of array elements returned |

  `Examples:`
  
  ```javascript
  // Element match projection
  db.products.find({}, {
    name: 1,
    tags: {
      $elemMatch: { $in: ["samsung", "logitech"] }
    }
  })
  
  // Positional operator
  db.products.find(
    { tags: { $exists: true } },
    { name: 1, "tags.$": 1 }
  )
  
  // Slice operator
  db.products.find(
    { tags: { $exists: true } },
    { name: 1, tags: { $slice: 2 } }
  )
  ```

- ### Query Modifiers

  `Cheatsheet:`
  
  | Function | Description |
  |----------|-------------|
  | `count()` | Get count of query results |
  | `limit(size)` | Limit number of results |
  | `skip(size)` | Skip first N results |
  | `sort(query)` | Sort results |

  `Examples:`
  
  ```javascript
  // Count
  db.products.find({}).count()
  
  // Limit
  db.products.find({}).limit(4)
  
  // Skip
  db.products.find({}).skip(2)
  
  // Limit and skip (pagination)
  db.products.find({}).limit(4).skip(2)
  
  // Sort (1 = ascending, -1 = descending)
  db.products.find({}).sort({
    category: 1,
    name: -1
  })
  ```

---

## ✏️ Update Documents

- ### Update Functions

  `Cheatsheet:`
  
  | Function | Description |
  |----------|-------------|
  | `updateOne()` | Update single document |
  | `updateMany()` | Update multiple documents |
  | `replaceOne()` | Replace entire document |
  
  `Update One Example`
  
    ```javascript
    db.products.updateOne(
      { _id: 1 },
      { $set: { category: "food" } }
    )
    ```
  
  `Update Many Example`
  
  ```javascript
  db.products.updateMany(
    {
      $and: [
        { category: { $eq: "food" } },
        { tags: { $exists: false } }
      ]
    },
    { $set: { tags: ["food"] } }
  )
  ```
  
  `Replace One Example`
  
  ```javascript
  db.products.replaceOne(
    { _id: 9 },
    {
      name: "Adidas",
      price: new NumberLong("1100000"),
      category: "shoes",
      tags: ["adidas", "shoes", "running"]
    }
  )
  ```

- ### Field Update Operators

  `Cheatsheet:`
  
  | Operator | Description |
  |----------|-------------|
  | `$set` | Set field value |
  | `$unset` | Remove field |
  | `$rename` | Rename field |
  | `$inc` | Increment numeric field |
  | `$currentDate` | Set field to current date |
  
  `Examples:`
  
  ```javascript
  // Set field
  db.products.updateMany({}, {
    $set: { stock: 0 }
  })
  
  // Increment field
  db.products.updateMany({}, {
    $inc: { stock: 10 }
  })
  
  // Rename field
  db.customers.updateMany({}, {
    $rename: { name: "full_name" }
  })
  
  // Unset field
  db.customers.updateMany({}, {
    $unset: { wrong: "" }
  })
  
  // Current date
  db.products.updateMany({}, {
    $currentDate: {
      lastModifiedDate: { $type: "date" }
    }
  })
  ```

- ### Array Update Operators

  `Cheatsheet:`
  
  | Operator | Description |
  |----------|-------------|
  | `$` | Update first array element matching condition |
  | `$[]` | Update all array elements |
  | `$[<identifier>]` | Update array elements matching arrayFilters |
  | `<index>` | Update array element at specific index |
  | `$addToSet` | Add value to array (if not exists) |
  | `$pop` | Remove first (-1) or last (1) array element |
  | `$pull` | Remove all matching array elements |
  | `$push` | Add element to array |
  | `$pullAll` | Remove all specified elements from array |
  
  `Examples:`
  
  ```javascript
  // Update first matching element
  db.products.updateMany(
    { ratings: 90 },
    { $set: { "ratings.$": 100 } }
  )
  
  // Update all elements
  db.products.updateMany({}, {
    $set: { "ratings.$[]": 100 }
  })
  
  // Update with array filter
  db.products.updateMany(
    {},
    { $set: { "ratings.$[element]": 100 } },
    { arrayFilters: [{ element: { $gte: 80 } }] }
  )
  
  // Update by index
  db.products.updateMany({}, {
    $set: {
      "ratings.0": 50,
      "ratings.1": 60
    }
  })
  
  // Add to set
  db.products.updateOne(
    { _id: 1 },
    { $addToSet: { tags: "popular" } }
  )
  
  // Pop (remove first)
  db.products.updateOne(
    { _id: 1 },
    { $pop: { ratings: -1 } }
  )
  
  // Pop (remove last)
  db.products.updateOne(
    { _id: 1 },
    { $pop: { ratings: 1 } }
  )
  
  // Pull with condition
  db.products.updateMany({}, {
    $pull: { ratings: { $gte: 80 } }
  })
  
  // Push element
  db.products.updateMany({}, {
    $push: { ratings: 100 }
  })
  
  // Pull all
  db.products.updateMany({}, {
    $pullAll: { ratings: [100] }
  })
  ```

- ### Array Update Modifiers

  `Cheatsheet:`
  
  | Modifier | Description |
  |----------|-------------|
  | `$each` | Add multiple elements with $addToSet or $push |
  | `$position` | Specify position for adding data with $push |
  | `$slice` | Limit maximum array size with $push |
  | `$sort` | Sort array after $push operation |
  
  `Examples:`
  
  ```javascript
  // Push with each
  db.products.updateMany({}, {
    $push: {
      ratings: { $each: [100, 200, 300] }
    }
  })
  
  // Add to set with each
  db.products.updateMany({}, {
    $addToSet: {
      tags: { $each: ["trending", "popular"] }
    }
  })
  
  // Push with position
  db.products.updateMany({}, {
    $push: {
      tags: {
        $each: ["hot"],
        $position: 1
      }
    }
  })
  
  // Push with sort
  db.products.updateMany({}, {
    $push: {
      ratings: {
        $each: [100, 200, 300, 400, 500],
        $sort: -1
      }
    }
  })
  
  // Push with slice
  db.products.updateMany({}, {
    $push: {
      ratings: {
        $each: [100, 200, 300, 400, 500],
        $slice: -3
      }
    }
  })
  ```

---

## 🗑️ Delete Documents

- **Delete Functions**

  | Function | Description |
  |----------|-------------|
  | `deleteOne(query)` | Delete single matching document |
  | `deleteMany(query)` | Delete all matching documents |
  
  **Warning**: Deleted documents cannot be recovered.

- **Examples**

  `Delete One`
  
  ```javascript
  db.customers.insertOne({
    _id: "spamer",
    full_name: "Spammer"
  })
  
  db.customers.deleteOne({
    _id: "spamer"
  })
  ```
  
  `Delete Many`
  
  ```javascript
  db.customers.insertMany([
    { _id: "spammer1", full_name: "Spammer1" },
    { _id: "spammer2", full_name: "Spammer2" },
    { _id: "spammer3", full_name: "Spammer3" }
  ])
  
  db.customers.deleteMany({
    _id: { $regex: "spammer" }
  })
  ```

---

## 📦 Bulk Operations

Bulk operations allow sending multiple commands in a single request, which is much faster than individual requests.

- **Bulk Functions**

  | Function | Description |
  |----------|-------------|
  | `insertMany()` | Insert multiple documents |
  | `updateMany()` | Update multiple documents |
  | `deleteMany()` | Delete multiple documents |
  | `bulkWrite()` | Perform multiple write operations |

- **Example**

  ```javascript
  db.customers.bulkWrite([
    {
      insertOne: {
        document: {
          _id: "dzaru",
          full_name: "Dzaru"
        }
      }
    },
    {
      insertOne: {
        document: {
          _id: "rizky",
          full_name: "Rizky"
        }
      }
    },
    {
      updateMany: {
        filter: {
          _id: { $in: ["dzaru", "rizky"] }
        },
        update: {
          $set: {
            full_name: "Dzaru Rizky Fathan Fortuna"
          }
        }
      }
    }
  ])
  ```

---

## 🚀 Indexes

Indexes make queries more efficient by allowing MongoDB to find data without scanning the entire collection.

- **Index Features**

  - **Efficient queries** - Speeds up data retrieval
  - **B-Tree structure** - Uses balanced tree data structure by default
  - **Sorted storage** - Stores and sorts specific fields
  - **Range queries** - Facilitates searching and pagination

- **Index Functions**

  | Function | Description |
  |----------|-------------|
  | `createIndex()` | Create index on collection |
  | `getIndexes()` | View all indexes on collection |
  | `dropIndex()` | Delete index from collection |

- **Single Field Index**

  ```javascript
  // Create index on category field
  db.products.createIndex({ category: 1 })
  
  // View all indexes
  db.products.getIndexes()
  
  // Query using index
  db.products.find({ category: "food" })
  
  // Explain query execution
  db.products.find({ category: "food" }).explain()
  ```

- **Compound Index**

  Create indexes on multiple fields (maximum 32 fields).
  
  ```javascript
  // Create compound index
  db.products.createIndex({
    stock: 1,
    tags: 1
  })
  
  // Queries that use the index
  db.products.find({ stock: 10, tags: "popular" })
  db.products.find({ stock: 10 })
  
  // Query that doesn't use the index efficiently
  db.products.find({ tags: "popular" })
  ```

 - **Index Strategy**

    - Create indexes to support query performance
    - Use single index for queries on one field
    - Use compound index for queries on multiple fields
    - Compound index (a, b, c) supports queries: a, a+b, a+b+c
    - Use `explain()` to verify index usage
    - Create indexes for sorting results

- **Text Index**

  Text indexes enable full-text search on string fields.
  
  ```javascript
  // Create text index with weights
  db.products.createIndex(
    {
      name: "text",
      category: "text",
      tags: "text"
    },
    {
      weights: {
        name: 10,
        category: 5,
        tags: 1
      }
    }
  )
  
  // Search for single term
  db.products.find({
    $text: { $search: "mie" }
  })
  
  // Search for multiple terms
  db.products.find({
    $text: { $search: "mie laptop" }
  })
  
  // Search for exact phrase
  db.products.find({
    $text: { $search: '"mie sedap"' }
  })
  
  // Exclude terms
  db.products.find({
    $text: { $search: "mie -sedap" }
  })
  
  // Get text score
  db.products.find(
    { $text: { $search: "mie" } },
    { searchScore: { $meta: "textScore" } }
  )
  ```

- **Wildcard Index**

  Create indexes on unknown or frequently changing fields.
  
  ```javascript
  // Create wildcard index
  db.customers.createIndex({
    "customFields.$**": 1
  })
  
  // Insert documents with different custom fields
  db.customers.insertMany([
    {
      _id: "budi",
      full_name: "Budi",
      customFields: {
        hobby: "Gaming",
        university: "Universitas Belum Ada"
      }
    },
    {
      _id: "rully",
      full_name: "Rully",
      customFields: {
        ipk: 3.2,
        university: "Universitas Belum Ada"
      }
    }
  ])
  
  // Query custom fields
  db.customers.find({
    "customFields.hobby": "Gaming"
  })
  ```

- **Index Properties**

  `TTL Index (Time To Live)` : &nbsp;Automatically delete documents after a specified time.
  
  ```javascript
  // Create TTL index
  db.sessions.createIndex(
    { createdAt: 1 },
    { expireAfterSeconds: 10 }
  )
  
  // Insert document
  db.sessions.insertOne({
    _id: 1,
    session: "Session 1",
    createdAt: new Date()
  })
  // Document will be deleted after 10 seconds
  // Background process runs every 60 seconds
  ```
  
  `Unique Index` : &nbsp;Ensure field values are unique across collection.
  
  ```javascript
  // Create unique index
  db.customers.createIndex(
    { email: 1 },
    { unique: true, sparse: true }
  )
  
  // Insert unique values
  db.customers.updateOne(
    { _id: "dzaru" },
    { $set: { email: "dzaru@example.com" } }
  )
  ```

  `Case Insensitive Index` : &nbsp;Create case-insensitive indexes using collation.
  
  ```javascript
  // Create case-insensitive index
  db.customers.createIndex(
    { full_name: 1 },
    {
      collation: {
        locale: "en",
        strength: 2
      }
    }
  )
  
  // Query with collation
  db.customers.find({
    full_name: "DZARU RIZKY FATHAN FORTUNA"
  }).collation({
    locale: "en",
    strength: 2
  })
  ```

- **Partial Index**

  Create indexes with filter conditions.
  
  ```javascript
  // Create partial index
  db.products.createIndex(
    { price: 1 },
    {
      partialFilterExpression: {
        stock: { $gt: 0 }
      }
    }
  )
  
  // Query using partial index
  db.products.find({
    price: { $eq: 2500 },
    stock: { $gt: 0 }
  })
  ```

---

## 🔒 Security

- **Enable Access Control**

  By default, MongoDB runs without authentication or authorization. To secure MongoDB:
  
  - Create Admin User
  - Restart with --auth flag

- **Create Admin User**

  ```javascript
  use admin
  
  db.createUser({
    user: "mongo",
    pwd: "mongo",
    roles: [
      "userAdminAnyDatabase",
      "readWriteAnyDatabase"
    ]
  })
  ```

- **Restart MongoDB with Auth**

  ```bash
  bin/mongod --auth --dbpath=/path/to/data
  ```

- **Connect with Authentication**

  ```bash
  bin/mongosh "mongodb://username:password@host:port/database?authSource=admin"
  ```

---

## 👤 User Management

- **User Functions**

  | Function | Description |
  |----------|-------------|
  | `db.createUser()` | Create user |
  | `db.getUsers()` | Get all users |
  | `db.dropUser()` | Delete user |
  | `db.updateUser()` | Update user |
  | `db.changeUserPassword()` | Change user password |

- **Create Users**

  ```javascript
  // Create read-only user
  db.createUser({
    user: "example",
    pwd: "example",
    roles: [
      { role: "read", db: "study" }
    ]
  })
  
  // Create read-write user
  db.createUser({
    user: "example2",
    pwd: "example2",
    roles: [
      { role: "readWrite", db: "study" }
    ]
  })
  ```

- **Update Users**

  ```javascript
  // Change password
  db.changeUserPassword("example", "secret")
  
  // Delete user
  db.dropUser("example")
  
  // Update roles
  db.updateUser("example2", {
    roles: [
      { role: "readWrite", db: "test" },
      { role: "readWrite", db: "study" }
    ]
  })
  ```
  
- **Roles**

  Roles define the access permissions of a user in the database, specifying what actions can be performed on data and system resources.
  
  `Built-in Roles`

  | Role | Description |
  |------|-------------|
  | `read` | Read data from all non-system collections |
  | `readWrite` | Read and modify data in all non-system collections |
  | `dbAdmin` | Database administration capabilities |
  | `userAdmin` | Create and manage users and roles |
  | `dbOwner` | Combination of readWrite, dbAdmin, and userAdmin |

  `Backup & Restore Roles`

  | Role | Description |
  |------|-------------|
  | `backup` | Perform database backup |
  | `restore` | Perform database restore |

  `Superuser Role`

  | Role | Description |
  |------|-------------|
  | `root` | Complete access to all resources |

- **Custom Roles**

  Create custom roles with specific privileges at the collection level.

  `Role Functions`
  
  | Function | Description |
  |----------|-------------|
  | `db.createRole()` | Create custom role |
  | `db.getRoles()` | Get all roles |
  | `db.deleteRole()` | Delete role |
  | `db.updateRole()` | Update role |

  `Create Custom Role`

  ```javascript
  // Create custom role with specific privileges
  db.createRole({
    role: "session_management",
    privileges: [
      {
        resource: {
          db: "study",
          collection: "sessions"
        },
        actions: ["insert"]
      }
    ],
    roles: [
      { role: "read", db: "study" }
    ]
  })
  
  // View roles with privileges
  db.getRoles({ showPrivileges: true })
  
  // Create user with custom role
  db.createUser({
    user: "fathan",
    pwd: "fathan",
    roles: ["session_management"]
  })
  ```

  `Test Custom Role Permissions`
  
  ```javascript
  // Allowed: read
  db.sessions.find()
  
  // Allowed: insert
  db.sessions.insertOne({
    _id: "test",
    name: "test"
  })
  
  // Not allowed: update
  db.sessions.updateOne(
    { _id: "test" },
    { $set: { name: "change" } }
  )
  
  // Not allowed: delete
  db.sessions.deleteOne({ _id: "test" })
  ```

---

## 💾 Backup & Restore

MongoDB provides separate command-line tools for backup and restore operations.

**Download**: `https://www.mongodb.com/try/download/database-tools`

- ### Backup Tools

  - **mongodump**
  
    - **Format**: Binary format
    - **Use case**: Databases with binary data or very large size
    - **Scope**: Entire database
  
    `Usage:`
  
    ```bash
    bin/mongodump \
      --host=localhost \
      --port=27017 \
      --username=mongo \
      --password="mongo" \
      --authenticationDatabase=admin \
      --db=study \
      --out=backup-dump
    ```
  
  - **mongoexport**
  
    - **Format**: JSON or CSV
    - **Use case**: Text-only databases with moderate size
    - **Scope**: Per collection
  
    `Usage:`
    
    ```bash
    bin/mongoexport \
      --host=localhost \
      --port=27017 \
      --username=mongo \
      --password="mongo" \
      --authenticationDatabase=admin \
      --db=study \
      --collection=customers \
      --out=customers.json
    ```

- ### Restore Tools

  - **mongorestore**
  
    Restore backups created with `mongodump`
  
    `Usage:`
    
    ```bash
    bin/mongorestore \
      --host=localhost \
      --port=27017 \
      --username=mongo \
      --password="mongo" \
      --authenticationDatabase=admin \
      --db=study-restore \
      backup-dump/study
    ```
  
  - **mongoimport**
  
    Restore backups created with `mongoexport`
  
    `Usage:`
    
    ```bash
    bin/mongoimport \
      --host=localhost \
      --port=27017 \
      --username=mongo \
      --password="mongo" \
      --authenticationDatabase=admin \
      --db=study-import \
      --collection=customers \
      --file=customers.json
    ```

- ### Backup Strategy

  `Choose mongodump when:`
  - Database contains binary data  
  - Database size is very large  
  - Need to backup entire database structure  
  - Need faster restore process  
  
  `Choose mongoexport when:`
  
  - Database contains only text data  
  - Database size is moderate  
  - Need human-readable backup format  
  - Need to backup specific collections  
  - Need to import data into other systems  

---

## 💡 Best Practices

- ### Data Modeling
  
  - `Embed When`
     - Data is always accessed together
     - Data has 1-to-1 or 1-to-few relationships
     - Data doesn't change frequently
  
  - `Reference When`
     - Data is accessed independently
     - Data has 1-to-many or many-to-many relationships
     - Data changes frequently

- ### Indexing

  - `Create indexes for`
     - Frequently queried fields
     - Fields used in sorting
     - Fields used in range queries
  
  - `Avoid over-indexing`
     - Each index requires storage space
     - Indexes slow down write operations
     - Only create necessary indexes
  
  - `Monitor index usage`
     - Use `explain()` to verify index usage
     - Remove unused indexes
     - Review index performance regularly

- ### Security

  - `Always enable authentication`
     - Never run MongoDB without `--auth` in production
     - Use strong passwords
     - Create specific users for each application

  - `Follow principle of least privilege`
     - Give users only necessary permissions
     - Use custom roles for fine-grained control
     - Avoid using admin accounts for applications

  - `Network security`
     - Bind MongoDB to localhost when possible
     - Use firewall rules to restrict access
     - Enable SSL/TLS for network encryption

- ### Performance

  - `Query optimization`
     - Use indexes effectively
     - Limit returned fields with projection
     - Use pagination for large result sets

  - `Connection management`
     - Use connection pooling
     - Close connections properly
     - Monitor connection count

  - `Schema design`
     - Avoid deep nesting (max 100 levels, but aim for less)
     - Keep document size under 16MB
     - Design schema based on query patterns

- ### Backup

  - `Regular backups`
     - Schedule automated backups
     - Test restore procedures regularly
     - Store backups in secure location

  - `Backup strategy`
     - Use mongodump for complete backups
     - Use mongoexport for data migration
     - Keep multiple backup versions

- ### Monitoring

  - `Monitor key metrics`
     - Query performance
     - Index usage
     - Database size
     - Connection count
     - Replication lag (if using replica sets)

  - `Use built-in tools`
     - `db.stats()` for database statistics
     - `db.serverStatus()` for server metrics
     - `explain()` for query analysis