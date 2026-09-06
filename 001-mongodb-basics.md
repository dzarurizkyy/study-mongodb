# 🍃 MongoDB Basics

A practical reference guide for learning MongoDB from scratch — covering installation, databases and collections, data modeling, BSON types, the full CRUD surface, query operators, indexes, security, user management, and backup & restore, worked through hands-on in `mongosh`.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [What Is MongoDB?](#what-is-mongodb)
  - [Terminology at a Glance](#terminology-at-a-glance)
- [Installation & Setup](#-installation--setup)
  - [Downloading MongoDB](#downloading-mongodb)
  - [Running the Server](#running-the-server)
  - [MongoDB Clients](#mongodb-clients)
- [Databases](#-databases)
  - [Creating & Selecting a Database](#creating--selecting-a-database)
  - [Database Methods](#database-methods)
- [Collections](#-collections)
  - [Database-Level Methods](#database-level-methods)
  - [Collection-Level Methods](#collection-level-methods)
- [Data Modeling](#-data-modeling)
  - [Schema Flexibility](#schema-flexibility)
  - [The Primary Key (_id)](#the-primary-key-_id)
  - [Embedded Documents](#embedded-documents)
  - [Document References](#document-references)
  - [Choosing Between Them](#choosing-between-them)
- [BSON Data Types](#-bson-data-types)
  - [Supported Types](#supported-types)
  - [ObjectId](#objectid)
  - [Date & ISODate](#date--isodate)
- [Insert Documents](#-insert-documents)
  - [Insert Functions](#insert-functions)
  - [Inserting a Single Document](#inserting-a-single-document)
  - [Inserting Multiple Documents](#inserting-multiple-documents)
  - [Inserting Embedded Documents](#inserting-embedded-documents)
- [Query Documents](#-query-documents)
- [Query Operators](#-query-operators)
  - [Comparison Operators](#comparison-operators)
  - [Logical Operators](#logical-operators)
  - [Element Operators](#element-operators)
  - [Evaluation Operators](#evaluation-operators)
  - [Array Operators](#array-operators)
  - [Projection Operators](#projection-operators)
  - [Cursor Methods](#cursor-methods)
- [Update Documents](#-update-documents)
  - [Update Functions](#update-functions)
  - [Field Update Operators](#field-update-operators)
  - [Array Update Operators](#array-update-operators)
  - [Array Update Modifiers](#array-update-modifiers)
- [Delete Documents](#-delete-documents)
- [Bulk Operations](#-bulk-operations)
- [Indexes](#-indexes)
  - [Why Indexes Matter](#why-indexes-matter)
  - [Index Functions](#index-functions)
  - [Single Field Index](#single-field-index)
  - [Compound Index](#compound-index)
  - [Text Index](#text-index)
  - [Wildcard Index](#wildcard-index)
  - [Index Properties](#index-properties)
  - [Partial Index](#partial-index)
  - [Index Strategy](#index-strategy)
- [Security](#-security)
  - [Enabling Access Control](#enabling-access-control)
  - [Creating an Admin User](#creating-an-admin-user)
  - [Connecting with Authentication](#connecting-with-authentication)
- [User Management](#-user-management)
  - [User Functions](#user-functions)
  - [Creating Users](#creating-users)
  - [Updating Users](#updating-users)
  - [Built-in Roles](#built-in-roles)
  - [Custom Roles](#custom-roles)
- [Backup & Restore](#-backup--restore)
  - [Backup Tools](#backup-tools)
  - [Restore Tools](#restore-tools)
  - [Choosing a Strategy](#choosing-a-strategy)
- [Query Execution Flow](#-query-execution-flow)
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

### What Is MongoDB?

- **MongoDB** is a free and open-source database management system
- It is a **document-based database** — data is stored in flexible, JSON-like documents instead of rows in a table
- Developed by **10gen** in 2007 and publicly released in 2009; the company later renamed itself **MongoDB Inc**
- Nearly every programming language has an official client (driver), so it slots into any stack
- Documents are manipulated with **JavaScript**, not SQL — the shell is a real JavaScript runtime

> Reference: [mongodb.com](https://www.mongodb.com/) · [mongodb.com/docs](https://www.mongodb.com/docs/)

### Terminology at a Glance

The mental model is close to a relational database, only the names — and the flexibility — change:

| Relational database | MongoDB |
| --- | --- |
| Database | **Database** |
| Table | **Collection** |
| Row / record | **Document** |
| Column | **Field** |
| Primary key | **`_id` field** |
| `JOIN` | **Embedded document** or a manual **reference** |

> **Key Insight:** the schema does not live in the database — it lives in your documents. That is what makes MongoDB flexible, and also what makes [data modeling](#-data-modeling) the decision that matters most.

---

## 📦 Installation & Setup

### Downloading MongoDB

- Download MongoDB as an archive file
- Extract the downloaded file anywhere on your computer
- Inside the archive you'll find `bin/mongod` — the MongoDB **daemon**, the server process itself

> Reference: [mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)

### Running the Server

MongoDB does not create its own storage folder, so make one first and point the daemon at it:

```bash
# Create the data directory
mkdir -p /path/to/data

# Start the MongoDB server
bin/mongod --dbpath=/path/to/data
```

Stop the server with `Ctrl + C`.

> **Note:** `--dbpath` is where every database physically lives. Point a second `mongod` at the same folder and it will refuse to start — the data directory is locked by whichever process owns it.

### MongoDB Clients

Unlike MySQL or PostgreSQL, MongoDB ships **only** as a server application. To actually run commands you need a separate client.

| Client | Best for | Download |
| --- | --- | --- |
| **MongoDB Compass** | Free GUI client — browse data visually and run commands | [compass](https://www.mongodb.com/products/tools/compass) |
| **MongoDB Shell** (`mongosh`) | Terminal client for Linux servers, headless systems, and scripting | [shell](https://www.mongodb.com/products/tools/shell) |

MongoDB listens on port **`27017`** by default, which is what both clients pre-fill.

```bash
# Extract the downloaded archive, then run mongosh from the bin directory
bin/mongosh mongodb://host:port/database_name
```

---

## 🗄️ Databases

A **database** is the storage location for collections. It provides logical separation — usually one database per application — and MongoDB creates it automatically the first time you write to it.

### Creating & Selecting a Database

```javascript
// Switch to a database (creates it if it doesn't exist)
use study

// Show all databases
show databases
```

> **Note:** `use study` does not create anything on disk yet. The database only materialises once the first document is inserted, which is why a brand-new database is missing from `show databases`.

### Database Methods

| Method | Description |
| --- | --- |
| `db.dropDatabase()` | Delete the database |
| `db.getName()` | Get the database name |
| `db.hostInfo()` | Get host information |
| `db.version()` | Get the database version |
| `db.stats()` | Get database statistics |

---

## 📚 Collections

A **collection** stores documents. Two hard limits are worth memorising: **16 MB** maximum per document, and a maximum nesting depth of **100** levels.

### Database-Level Methods

| Method | Description |
| --- | --- |
| `db.getCollectionNames()` | Get all collection names |
| `db.createCollection(name)` | Create a new collection |
| `db.getCollection(name)` | Get a collection object |
| `db.<name>` | Shorthand for `db.getCollection(<name>)` |
| `db.getCollectionInfos()` | Get information about all collections |

### Collection-Level Methods

| Method | Description |
| --- | --- |
| `db.<collection>.find()` | Get all documents |
| `db.<collection>.count()` | Get the document count |
| `db.<collection>.drop()` | Delete the collection |
| `db.<collection>.totalSize()` | Get the total collection size |
| `db.<collection>.stats()` | Get collection statistics |

> **Tip:** `db.createCollection()` is optional — inserting into a collection that doesn't exist creates it. Call it explicitly only when you need options such as a capped collection or a validation schema.

---

## 🏗️ Data Modeling

### Schema Flexibility

- **No predefined schema required** — insert data directly without declaring a structure first
- **Flexible documents** — each document in a collection can carry different fields
- **Best practice** — still use consistent data types across documents in the same collection

> **Key Insight:** "schemaless" describes the *database*, not your *application*. The application always assumes a shape; keeping that shape consistent is a discipline MongoDB leaves to you.

### The Primary Key (_id)

- **Mandatory field** — every document must have an `_id`
- **Single field only** — composite primary keys do not exist
- **Auto-generated** — MongoDB creates an [ObjectId](#objectid) when `_id` is not provided

### Embedded Documents

Related data is stored *inside* the parent document:

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

### Document References

The same data split across collections, linked by a field that holds the parent's `_id`:

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

### Choosing Between Them

| Use **Embedded** when | Use **Reference** when |
| --- | --- |
| Documents are interdependent | Documents can stand alone |
| You cannot directly modify the embedded document | You can directly manipulate the referenced document |
| The nested data is always needed when reading the parent | The related data is not always needed when reading |

> **Note:** references are not joins. Nothing enforces that `user_id` points at a real user, and reading both sides means either two queries or an aggregation `$lookup`. That cost is the whole reason to embed when you can.

---

## 📊 BSON Data Types

**BSON** (Binary JSON) is the binary-encoded serialization format MongoDB uses for JSON-like documents. It adds the types JSON lacks — dates, 64-bit integers, binary data, and `ObjectId`.

### Supported Types

| Type | BSON Type |
| --- | --- |
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

### ObjectId

- **Random and unique** value
- **Fast to generate** and naturally ordered
- **12 bytes**: 4 bytes timestamp + 5 bytes random value + 3 bytes incrementing counter
- **Default `_id`** — used as the primary key whenever you don't supply one

> **Tip:** because the first 4 bytes are a timestamp, sorting by `_id` sorts roughly by creation time — no separate `createdAt` index needed for that.

### Date & ISODate

- **BSON Date** — a 64-bit integer holding milliseconds since the Unix epoch (1 January 1970)
- **ISODate** — how MongoDB displays and accepts that value
- **JavaScript compatible** — interchangeable with the JavaScript `Date` object

---

## ➕ Insert Documents

### Insert Functions

| Function | Description |
| --- | --- |
| `db.<collection>.insertOne(document)` | Insert a single document |
| `db.<collection>.insertMany(array<document>)` | Insert multiple documents |

### Inserting a Single Document

```javascript
db.customers.insertOne({
  _id: "dzaru",
  name: "Dzaru Rizky Fathan Fortuna"
})
```

### Inserting Multiple Documents

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

### Inserting Embedded Documents

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

> **Note:** a bare number in the shell becomes a `double`. Wrap it in `NumberLong()`, `NumberInt()` or `NumberDecimal()` when the field is meant to be an integer or exact decimal — money especially.

---

## 🔍 Query Documents

`find()` takes a filter document: every field you list must match for the document to be returned.

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

> **Key Insight:** dot notation (`"items.product_id"`) reaches into embedded documents *and* into arrays of embedded documents — the filter matches if **any** array element matches. To require several conditions on the *same* element, use [`$elemMatch`](#array-operators).

---

## 🔧 Query Operators

### Comparison Operators

| Operator | Description |
| --- | --- |
| `$eq` | Equals |
| `$gt` | Greater than |
| `$gte` | Greater than or equal |
| `$lt` | Less than |
| `$lte` | Less than or equal |
| `$in` | Matches any value in an array |
| `$nin` | Matches none of the values in an array |
| `$ne` | Not equal |

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

> **Note:** listing two fields side by side is already an implicit AND — the third example matches only documents satisfying both conditions.

### Logical Operators

| Operator | Description |
| --- | --- |
| `$and` | Joins queries with logical AND |
| `$or` | Joins queries with logical OR |
| `$nor` | Joins queries with logical NOR |
| `$not` | Inverts a query expression |

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

> **Tip:** you only need `$and` explicitly when the same field appears twice — `{ $and: [{ price: { $gt: 100 } }, { price: { $lt: 500 } }] }`. Otherwise the implicit AND is shorter and reads better.

### Element Operators

| Operator | Description |
| --- | --- |
| `$exists` | Matches documents that have (or lack) the specified field |
| `$type` | Matches documents where the field has the specified BSON type |

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

> **Key Insight:** `$exists` is the operator a flexible schema forces you to learn. Since documents in one collection may legitimately differ, "does this field exist at all?" becomes a real query.

### Evaluation Operators

| Operator | Description |
| --- | --- |
| `$expr` | Use aggregation expressions inside a query |
| `$jsonSchema` | Validate documents against a JSON schema |
| `$mod` | Perform a modulo operation |
| `$regex` | Match with a regular expression |
| `$text` | Perform a text search |
| `$where` | Match with a JavaScript function |

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

> **Note:** `$expr` is how you compare **two fields of the same document** — a plain filter can only compare a field against a constant. Note the `$` prefix: `"$_id"` means *the value of the `_id` field*, not the string.

> **Gotcha:** `$where` runs JavaScript for every document in the collection, so it can never use an index. Prefer `$expr` — it is both faster and safer.

### Array Operators

| Operator | Description |
| --- | --- |
| `$all` | Matches arrays containing all the specified elements |
| `$elemMatch` | Matches if a single array element meets all the conditions |
| `$size` | Matches arrays of the specified length |

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

### Projection Operators

Projection is the second argument to `find()` — it selects which fields come back.

| Operator | Description |
| --- | --- |
| `$` | Limit the array to the first matching element |
| `$elemMatch` | Limit the array to the first element matching a condition |
| `$meta` | Return metadata from matching documents |
| `$slice` | Control how many array elements are returned |

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

> **Tip:** `1` includes a field, `0` excludes it, and the two cannot be mixed in one projection — with the sole exception of `_id`, which you can always switch off via `{ _id: 0 }`.

### Cursor Methods

`find()` returns a cursor, and these methods refine it before the documents are fetched.

| Function | Description |
| --- | --- |
| `count()` | Get the count of query results |
| `limit(size)` | Limit the number of results |
| `skip(size)` | Skip the first N results |
| `sort(query)` | Sort the results |

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

> **Gotcha:** `skip()` gets slower the deeper you page — the server still walks every skipped document. For large collections, paginate on an indexed field instead (`{ _id: { $gt: lastSeenId } }`).

---

## ✏️ Update Documents

### Update Functions

| Function | Description |
| --- | --- |
| `updateOne()` | Update a single document |
| `updateMany()` | Update multiple documents |
| `replaceOne()` | Replace an entire document |

**Update one**

```javascript
db.products.updateOne(
  { _id: 1 },
  { $set: { category: "food" } }
)
```

**Update many**

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

**Replace one**

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

> **Gotcha:** `replaceOne()` swaps the whole document — every field you leave out is gone. `updateOne()` with `$set` only touches the fields you name. Reach for replace deliberately, never by accident.

### Field Update Operators

| Operator | Description |
| --- | --- |
| `$set` | Set a field value |
| `$unset` | Remove a field |
| `$rename` | Rename a field |
| `$inc` | Increment a numeric field |
| `$currentDate` | Set a field to the current date |

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

> **Note:** `$inc` is atomic on the server — read-modify-write in your application is not. For counters, stock levels and balances, always let `$inc` do the arithmetic.

### Array Update Operators

| Operator | Description |
| --- | --- |
| `$` | Update the first array element matching the query |
| `$[]` | Update all array elements |
| `$[<identifier>]` | Update array elements matching `arrayFilters` |
| `<index>` | Update the array element at a specific index |
| `$addToSet` | Add a value to the array only if it isn't already there |
| `$pop` | Remove the first (`-1`) or last (`1`) array element |
| `$pull` | Remove all array elements matching a condition |
| `$push` | Add an element to the array |
| `$pullAll` | Remove all the specified elements from the array |

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

> **Key Insight:** the positional `$` needs the array field to appear in the **query** — that is how MongoDB knows which element matched. `{ ratings: 90 }` in the filter is not decoration; drop it and `"ratings.$"` has nothing to point at.

### Array Update Modifiers

| Modifier | Description |
| --- | --- |
| `$each` | Add multiple elements with `$addToSet` or `$push` |
| `$position` | Specify the insert position for `$push` |
| `$slice` | Cap the array size after a `$push` |
| `$sort` | Sort the array after a `$push` |

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

> **Tip:** `$push` + `$each` + `$sort` + `$slice` together give you a leaderboard in one operation — append, re-sort, keep the top N, all atomically.

---

## 🗑️ Delete Documents

| Function | Description |
| --- | --- |
| `deleteOne(query)` | Delete a single matching document |
| `deleteMany(query)` | Delete all matching documents |

> ⚠️ **Warning:** deleted documents cannot be recovered. Run the same filter through `find()` first and look at what comes back.

**Delete one**

```javascript
db.customers.insertOne({
  _id: "spamer",
  full_name: "Spammer"
})

db.customers.deleteOne({
  _id: "spamer"
})
```

**Delete many**

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

> **Gotcha:** `deleteMany({})` with an empty filter deletes **every document** in the collection. There is no confirmation prompt.

---

## 📮 Bulk Operations

Bulk operations send several commands in a single request, which is dramatically faster than issuing them one at a time — one network round trip instead of N.

| Function | Description |
| --- | --- |
| `insertMany()` | Insert multiple documents |
| `updateMany()` | Update multiple documents |
| `deleteMany()` | Delete multiple documents |
| `bulkWrite()` | Perform multiple *different* write operations together |

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

> **Key Insight:** `bulkWrite()` is the only one of the four that mixes operation *types*. By default the operations run **in order** and stop at the first error — which is exactly why the inserts above can be followed by an update that depends on them.

---

## 🚀 Indexes

### Why Indexes Matter

An index lets MongoDB find data without scanning the whole collection.

- **Efficient queries** — speeds up data retrieval
- **B-Tree structure** — a balanced tree by default
- **Sorted storage** — stores the indexed fields already in order
- **Range queries** — makes range filters, sorting and pagination cheap

### Index Functions

| Function | Description |
| --- | --- |
| `createIndex()` | Create an index on the collection |
| `getIndexes()` | View all indexes on the collection |
| `dropIndex()` | Delete an index from the collection |

### Single Field Index

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

> **Tip:** `explain()` is the only honest answer to "is my index being used?". Look for `IXSCAN` in the winning plan — a `COLLSCAN` means MongoDB read every document.

### Compound Index

An index across multiple fields (maximum 32).

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

> **Key Insight:** a compound index works left to right — the **prefix rule**. An index on `(a, b, c)` serves queries on `a`, `a+b` and `a+b+c`, but not on `b` or `c` alone. Field order in `createIndex()` is a design decision, not a formality.

### Text Index

Text indexes enable full-text search on string fields, with per-field weights.

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

> **Note:** a collection can hold only **one** text index — so it has to cover every field you ever want to search. Multiple terms are OR-ed together; wrap them in escaped quotes for an exact phrase, and prefix with `-` to exclude.

### Wildcard Index

Indexes fields whose names you don't know ahead of time — user-defined custom fields, for example.

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

### Index Properties

**TTL Index (Time To Live)** — automatically delete documents after a set time.

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

**Unique Index** — enforce that a field's values are unique across the collection.

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

**Case Insensitive Index** — match regardless of letter case, using a collation.

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

> **Gotcha:** two details above are easy to miss. `sparse: true` is what lets a unique index coexist with documents that have no `email` at all — without it, the second document missing the field violates uniqueness. And a collation index is only used when the **query repeats the same collation**; omit `.collation(...)` and MongoDB falls back to a case-sensitive collection scan.

### Partial Index

Index only the documents matching a filter — a smaller index, and less write overhead.

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

> **Note:** the query must include the partial filter condition (`stock: { $gt: 0 }`), otherwise MongoDB cannot prove the index covers the result and will ignore it.

### Index Strategy

- Create indexes to support the queries you actually run
- Use a single-field index for queries on one field
- Use a compound index for queries on multiple fields
- Remember the prefix rule — a compound index on `(a, b, c)` supports `a`, `a+b`, `a+b+c`
- Create indexes for the fields you **sort** on, not just the ones you filter on
- Verify with `explain()` rather than assuming

---

## 🔒 Security

### Enabling Access Control

By default MongoDB runs with **no authentication and no authorization** — anyone who can reach the port has full access. Securing it is two steps:

1. Create an admin user
2. Restart `mongod` with the `--auth` flag

### Creating an Admin User

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

Then restart the server with access control turned on:

```bash
bin/mongod --auth --dbpath=/path/to/data
```

> **Key Insight:** the order matters. Create the admin user **before** enabling `--auth` — this is the "localhost exception", and once `--auth` is on with no users you are locked out of your own database.

### Connecting with Authentication

```bash
bin/mongosh "mongodb://username:password@host:port/database?authSource=admin"
```

> **Note:** `authSource=admin` says *where the user is defined*, which is not necessarily the database you're connecting to. Users created in `admin` always need it, and forgetting it is the most common "Authentication failed" cause.

---

## 👤 User Management

### User Functions

| Function | Description |
| --- | --- |
| `db.createUser()` | Create a user |
| `db.getUsers()` | Get all users |
| `db.dropUser()` | Delete a user |
| `db.updateUser()` | Update a user |
| `db.changeUserPassword()` | Change a user's password |

### Creating Users

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

### Updating Users

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

> **Gotcha:** `updateUser()` **replaces** the whole `roles` array rather than appending to it — the same trap as `replaceOne()`. Always pass the complete list of roles the user should end up with.

### Built-in Roles

A role defines what a user may do — which actions on which data and system resources.

**Database roles**

| Role | Description |
| --- | --- |
| `read` | Read data from all non-system collections |
| `readWrite` | Read and modify data in all non-system collections |
| `dbAdmin` | Database administration capabilities |
| `userAdmin` | Create and manage users and roles |
| `dbOwner` | Combination of `readWrite`, `dbAdmin` and `userAdmin` |

**Backup & restore roles**

| Role | Description |
| --- | --- |
| `backup` | Perform a database backup |
| `restore` | Perform a database restore |

**Superuser role**

| Role | Description |
| --- | --- |
| `root` | Complete access to all resources |

### Custom Roles

When the built-in roles are too coarse, define your own with privileges scoped down to a single collection.

| Function | Description |
| --- | --- |
| `db.createRole()` | Create a custom role |
| `db.getRoles()` | Get all roles |
| `db.deleteRole()` | Delete a role |
| `db.updateRole()` | Update a role |

**Creating a custom role**

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

**Testing what the role allows**

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

> **Key Insight:** a custom role composes two things — `privileges` (fine-grained actions on one resource) and `roles` (other roles it inherits). Above, `read` on `study` comes from inheritance, while `insert` on `sessions` alone comes from the explicit privilege.

---

## 💾 Backup & Restore

MongoDB ships backup and restore as **separate command-line tools**, downloaded independently of the server.

> Reference: [mongodb.com/try/download/database-tools](https://www.mongodb.com/try/download/database-tools)

### Backup Tools

**mongodump**

| Aspect | Detail |
| --- | --- |
| Format | Binary (BSON) |
| Use case | Databases with binary data, or a very large size |
| Scope | The entire database |

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

**mongoexport**

| Aspect | Detail |
| --- | --- |
| Format | JSON or CSV |
| Use case | Text-only databases of moderate size |
| Scope | One collection at a time |

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

### Restore Tools

Each backup tool has exactly one matching restore tool — they are not interchangeable.

**mongorestore** — restores a backup created with `mongodump`

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

**mongoimport** — restores a backup created with `mongoexport`

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

> **Note:** `--db` on restore does not have to match the original name — the examples above restore `study` into `study-restore` and `study-import`. That is the safe way to verify a backup without touching live data.

### Choosing a Strategy

| Choose **mongodump** when | Choose **mongoexport** when |
| --- | --- |
| The database contains binary data | The database contains only text data |
| The database is very large | The database is moderate in size |
| You need the whole database structure backed up | You need specific collections only |
| You want a faster restore | You want a human-readable backup |
| — | You need to import the data into another system |

---

## 🔁 Query Execution Flow

Knowing the order in which MongoDB processes a read is what makes the rest of this guide click. For every incoming query, the server runs:

```
Client (mongosh / driver)
  ↓
Authentication & Authorization    credentials → the user's role privileges
  ↓
Query Planner                     picks a plan, caches the winner
  ↓
Index Scan (IXSCAN)               ← if an index fits the filter
  or Collection Scan (COLLSCAN)   ← otherwise: every document is read
  ↓
Filter                            $eq, $gt, $in, $regex, $expr …
  ↓
Sort / Skip / Limit               free when the index already provides the order
  ↓
Projection                        only the requested fields are kept
  ↓
Cursor → Result
```

This explains the design decisions throughout this guide:

| Question | Answer |
| --- | --- |
| Why run `explain()` at all? | It reports the plan the planner chose — `IXSCAN` versus `COLLSCAN` is the whole story |
| Why index the fields you sort on? | Without an index the sort is *blocking*: results are buffered and sorted in memory, and the query fails once it exceeds the server's sort-memory limit |
| Why does a compound index on `(a, b)` not help a query on `b`? | The scan enters the B-tree from the left — no `a`, no entry point |
| Why is deep `skip()` slow? | Skipping happens after the scan, so the server still walks every skipped document |
| Why does projection come last? | It only reshapes documents that already matched — it never reduces the work of finding them |

---

## 🎯 Quick Reference

| Concept | Purpose | Key Syntax |
| --- | --- | --- |
| **Database** | Logical container for collections | `use study`, `show databases` |
| **Collection** | Container for documents (max 16 MB each) | `db.createCollection("products")` |
| **Insert** | Add documents | `insertOne(doc)`, `insertMany([...])` |
| **Query** | Read documents by filter | `db.products.find({ price: 2000 })` |
| **Comparison** | Match against a value | `$eq`, `$gt`, `$gte`, `$lt`, `$lte`, `$in`, `$nin`, `$ne` |
| **Logical** | Combine conditions | `$and`, `$or`, `$nor`, `$not` |
| **Element** | Match on presence or type | `$exists`, `$type` |
| **Evaluation** | Expressions, regex, text search | `$expr`, `$regex`, `$text`, `$mod`, `$jsonSchema` |
| **Array query** | Match inside arrays | `$all`, `$elemMatch`, `$size` |
| **Projection** | Choose the returned fields | `find(filter, { name: 1, _id: 0 })` |
| **Cursor** | Page and order results | `.sort({ name: 1 }).skip(10).limit(5)` |
| **Update** | Modify existing documents | `updateOne()`, `updateMany()`, `replaceOne()` |
| **Field update** | Change individual fields | `$set`, `$unset`, `$rename`, `$inc`, `$currentDate` |
| **Array update** | Change array contents | `$push`, `$pull`, `$addToSet`, `$pop`, `$[]`, `$[<id>]` |
| **Delete** | Remove documents | `deleteOne(query)`, `deleteMany(query)` |
| **Bulk** | Many writes, one round trip | `db.customers.bulkWrite([...])` |
| **Index** | Avoid collection scans | `db.products.createIndex({ category: 1 })` |
| **Compound index** | Serve multi-field queries (prefix rule) | `createIndex({ stock: 1, tags: 1 })` |
| **Special indexes** | Text, wildcard, TTL, unique, partial | `"text"`, `"$**"`, `expireAfterSeconds`, `unique`, `partialFilterExpression` |
| **Explain** | Inspect the chosen query plan | `db.products.find({...}).explain()` |
| **Auth** | Enable access control | `mongod --auth` + `db.createUser({...})` |
| **Roles** | Grant permissions | `read`, `readWrite`, `dbAdmin`, `userAdmin`, `dbOwner`, `root` |
| **Backup** | Export the data | `mongodump` (binary) / `mongoexport` (JSON, CSV) |
| **Restore** | Import the data back | `mongorestore` / `mongoimport` |

---

## 💡 Best Practices

**✅ Do This**

- **Embed data that is always read together** — 1-to-1 and 1-to-few relationships that rarely change belong inside the parent document
- **Reference data that is accessed independently** — 1-to-many and many-to-many relationships, and anything that changes frequently
- **Design the schema around your query patterns**, not around a normalized ideal — the queries are what you optimise for
- **Index the fields you filter, sort and range-scan on**, and order compound index fields to respect the prefix rule
- **Verify every index with `explain()`** and remove the ones nothing uses
- **Use consistent BSON types per field** — wrap integers in `NumberInt()` / `NumberLong()` and exact decimals in `NumberDecimal()`
- **Let the server do atomic arithmetic** with `$inc` instead of read-modify-write in application code
- **Always run MongoDB with `--auth` in production**, with strong passwords and a separate user per application
- **Follow the principle of least privilege** — custom roles for fine-grained control, and never an admin account for an application
- **Bind MongoDB to localhost where possible**, restrict the port with firewall rules, and enable SSL/TLS for network traffic
- **Limit returned fields with projection** and paginate large result sets
- **Use connection pooling**, close connections properly, and keep an eye on the connection count
- **Schedule automated backups, test the restore**, and keep multiple versions in a secure location
- **Monitor the numbers that matter** — query performance, index usage, database size, connection count, and replication lag on replica sets, via `db.stats()`, `db.serverStatus()` and `explain()`

**❌ Avoid This**

- **Running `deleteMany({})` or `updateMany({})` without checking the filter first** — there is no confirmation and no undo; preview with `find()`
- **Reaching for `replaceOne()` when you meant `updateOne()` with `$set`** — replace drops every field you didn't list
- **Passing a partial `roles` array to `updateUser()`** — it replaces the list rather than appending to it
- **Over-indexing** — every index costs storage and slows down every write
- **Sorting a large result set on an unindexed field** — the sort becomes blocking and fails past the server's memory limit
- **Paginating deep collections with `skip()`** — page on an indexed field such as `{ _id: { $gt: lastSeenId } }` instead
- **Using `$where` for anything an operator can express** — it runs JavaScript per document and can never use an index
- **Assuming a unique index tolerates missing fields** — add `sparse: true`, or the second document without the field is a duplicate
- **Querying a collation index without repeating the collation** — the index is silently skipped
- **Nesting documents deeply** — the ceiling is 100 levels and 16 MB, but a good schema stays far below both
- **Leaving MongoDB open on port `27017` with no authentication** — the default configuration is not a deployment configuration

> Reference: [mongodb.com/docs](https://www.mongodb.com/docs/) · [mongodb.com/docs/manual/crud](https://www.mongodb.com/docs/manual/crud/) · [mongodb.com/docs/manual/indexes](https://www.mongodb.com/docs/manual/indexes/)
