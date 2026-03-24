# 🍃 MongoDB Hands-On Practice — Student Management System

A complete hands-on scenario that covers all core MongoDB concepts: database & collection setup, data modeling, BSON types, insert, query operators, update operators, delete, bulk operations, indexes, security, user management, and backup & restore — all in one real-world student management use case.

---

## 📖 Scenario

You are building a database for **CampusDB** — a student management system for a university. You will manage:

- **Students** — personal data, contact, address, and custom fields
- **Courses** — subjects available for enrollment
- **Enrollments** — records linking students to courses with grades and sessions

By the end of this practice, you will have hands-on experience with every concept in the MongoDB guide.

---

## 🗂️ Table of Contents

1. [Setup & Verify](#1-setup--verify)
2. [Database & Collections](#2-database--collections)
3. [Insert Documents](#3-insert-documents)
4. [Query Documents](#4-query-documents)
5. [Query Operators](#5-query-operators)
6. [Update Documents](#6-update-documents)
7. [Delete Documents](#7-delete-documents)
8. [Bulk Operations](#8-bulk-operations)
9. [Indexes](#9-indexes)
10. [Security & User Management](#10-security--user-management)
11. [Backup & Restore](#11-backup--restore)
12. [Challenge Tasks](#-challenge-tasks)

---

## 1. Setup & Verify

### 1a. Start MongoDB Server

```bash
mkdir -p ~/campusdb-data
bin/mongod --dbpath=~/campusdb-data
```

### 1b. Connect with MongoDB Shell

```bash
bin/mongosh mongodb://localhost:27017
```

Verify the connection:

```javascript
db.version()
db.hostInfo()
```

---

## 2. Database & Collections

### 2a. Create and Select Database

```javascript
use campusdb

// Verify active database
db.getName()
```

### 2b. Create Collections

```javascript
db.createCollection("students")
db.createCollection("courses")
db.createCollection("enrollments")
```

Verify collections were created:

```javascript
db.getCollectionNames()
```

Expected output:

```
[ 'courses', 'enrollments', 'students' ]
```

Check collection stats:

```javascript
db.students.stats()
```

> ❓ **Notice** — MongoDB creates the database automatically on first use. The database won't appear in `show databases` until at least one document is inserted.

---

## 3. Insert Documents

### 3a. Insert a Single Student (insertOne)

```javascript
db.students.insertOne({
  _id: "dzaru",
  full_name: "Dzaru Rizky Fathan Fortuna",
  email: "dzaru@example.com",
  gender: "Male",
  birth_date: new Date("1998-03-15"),
  address: {
    street: "Jl. Merdeka No. 10",
    city: "Surabaya",
    province: "Jawa Timur",
    zip_code: "60111"
  },
  contact: {
    phone: "081217147620",
    emergency: "081234567890"
  },
  hobbies: ["Coding", "Gaming", "Reading"],
  gpa: new NumberDecimal("3.85"),
  enrolled_at: new Date("2024-02-01")
})
```

### 3b. Insert Multiple Courses (insertMany)

```javascript
db.courses.insertMany([
  {
    _id: new ObjectId(),
    code: "CS101",
    name: "Introduction to Programming",
    credits: new NumberInt("3"),
    category: "computer_science",
    tags: ["programming", "beginner", "fundamental"],
    max_students: new NumberInt("40"),
    tuition_fee: new NumberLong("1500000")
  },
  {
    _id: new ObjectId(),
    code: "CS201",
    name: "Data Structures & Algorithms",
    credits: new NumberInt("3"),
    category: "computer_science",
    tags: ["algorithm", "intermediate", "fundamental"],
    max_students: new NumberInt("35"),
    tuition_fee: new NumberLong("1750000")
  },
  {
    _id: new ObjectId(),
    code: "CS301",
    name: "Database Systems",
    credits: new NumberInt("3"),
    category: "computer_science",
    tags: ["database", "sql", "intermediate"],
    max_students: new NumberInt("30"),
    tuition_fee: new NumberLong("2000000")
  },
  {
    _id: new ObjectId(),
    code: "MATH101",
    name: "Calculus I",
    credits: new NumberInt("4"),
    category: "mathematics",
    tags: ["calculus", "beginner", "fundamental"],
    max_students: new NumberInt("50"),
    tuition_fee: new NumberLong("1200000")
  },
  {
    _id: new ObjectId(),
    code: "MATH201",
    name: "Linear Algebra",
    credits: new NumberInt("3"),
    category: "mathematics",
    tags: ["algebra", "intermediate"],
    max_students: new NumberInt("40"),
    tuition_fee: new NumberLong("1400000")
  }
])
```

### 3c. Insert More Students

```javascript
db.students.insertMany([
  {
    _id: "sariindah",
    full_name: "Sari Indah Permata",
    email: "sari@example.com",
    gender: "Female",
    birth_date: new Date("2000-07-22"),
    address: {
      street: "Jl. Kenanga No. 5",
      city: "Malang",
      province: "Jawa Timur",
      zip_code: "65141"
    },
    contact: {
      phone: "082112345678"
    },
    hobbies: ["Reading", "Painting"],
    gpa: new NumberDecimal("3.92"),
    enrolled_at: new Date("2024-02-01")
  },
  {
    _id: "budiprakoso",
    full_name: "Budi Prakoso Santoso",
    email: "budi@example.com",
    gender: "Male",
    birth_date: new Date("1999-11-08"),
    address: {
      street: "Jl. Mawar No. 3",
      city: "Bandung",
      province: "Jawa Barat",
      zip_code: "40111"
    },
    contact: {
      phone: "083198765432",
      emergency: "083111111111"
    },
    hobbies: ["Gaming", "Music", "Coding"],
    gpa: new NumberDecimal("3.45"),
    enrolled_at: new Date("2023-09-01")
  },
  {
    _id: "rizkyfathan",
    full_name: "Rizky Fathan Nugraha",
    email: "rizky@example.com",
    gender: "Male",
    birth_date: new Date("2001-05-17"),
    address: {
      street: "Jl. Anggrek No. 7",
      city: "Jakarta",
      province: "DKI Jakarta",
      zip_code: "10110"
    },
    contact: {
      phone: "081300011122"
    },
    hobbies: ["Football", "Reading"],
    gpa: new NumberDecimal("2.95"),
    enrolled_at: new Date("2024-02-01")
  },
  {
    _id: "indahsari",
    full_name: "Indah Sari Dewi",
    email: "indah@example.com",
    gender: "Female",
    birth_date: new Date("2000-01-30"),
    address: {
      street: "Jl. Dahlia No. 12",
      city: "Yogyakarta",
      province: "DI Yogyakarta",
      zip_code: "55111"
    },
    contact: {
      phone: "082299887766"
    },
    hobbies: ["Dancing", "Cooking", "Reading"],
    gpa: new NumberDecimal("3.70"),
    enrolled_at: new Date("2023-09-01")
  }
])
```

### 3d. Insert Enrollments with Embedded Sessions

```javascript
db.enrollments.insertMany([
  {
    _id: new ObjectId(),
    student_id: "dzaru",
    course_code: "CS101",
    semester: "2024-1",
    grade: new NumberInt("90"),
    ratings: [85, 88, 90, 92],
    sessions: [
      { week: 1, topic: "Hello World", attended: true },
      { week: 2, topic: "Variables & Data Types", attended: true },
      { week: 3, topic: "Control Flow", attended: false }
    ],
    enrolled_at: new Date("2024-02-10"),
    lastModifiedDate: new Date()
  },
  {
    _id: new ObjectId(),
    student_id: "dzaru",
    course_code: "CS301",
    semester: "2024-1",
    grade: new NumberInt("85"),
    ratings: [80, 82, 85, 88],
    sessions: [
      { week: 1, topic: "Introduction to DBMS", attended: true },
      { week: 2, topic: "Relational Model", attended: true }
    ],
    enrolled_at: new Date("2024-02-10"),
    lastModifiedDate: new Date()
  },
  {
    _id: new ObjectId(),
    student_id: "sariindah",
    course_code: "MATH101",
    semester: "2024-1",
    grade: new NumberInt("95"),
    ratings: [90, 93, 95, 97],
    sessions: [
      { week: 1, topic: "Limits", attended: true },
      { week: 2, topic: "Derivatives", attended: true }
    ],
    enrolled_at: new Date("2024-02-10"),
    lastModifiedDate: new Date()
  },
  {
    _id: new ObjectId(),
    student_id: "budiprakoso",
    course_code: "CS201",
    semester: "2023-2",
    grade: new NumberInt("78"),
    ratings: [70, 75, 78, 80],
    sessions: [
      { week: 1, topic: "Arrays & Linked Lists", attended: true },
      { week: 2, topic: "Stacks & Queues", attended: false },
      { week: 3, topic: "Trees", attended: true }
    ],
    enrolled_at: new Date("2023-09-10"),
    lastModifiedDate: new Date()
  },
  {
    _id: new ObjectId(),
    student_id: "rizkyfathan",
    course_code: "MATH101",
    semester: "2024-1",
    grade: new NumberInt("70"),
    ratings: [65, 68, 70, 72],
    sessions: [
      { week: 1, topic: "Limits", attended: false },
      { week: 2, topic: "Derivatives", attended: true }
    ],
    enrolled_at: new Date("2024-02-10"),
    lastModifiedDate: new Date()
  }
])
```

> ❓ **Notice** — enrollments use `student_id` as a reference to the students collection. This is the **Reference** pattern — enrollments can stand alone and students don't always need enrollment data when fetching their profile.

---

## 4. Query Documents

### 4a. Basic Find

```javascript
// Find all students
db.students.find()

// Find by _id
db.students.find({ _id: "dzaru" })

// Find by nested field
db.students.find({ "address.city": "Surabaya" })

// Find enrollments by student
db.enrollments.find({ student_id: "dzaru" })

// Find enrollments by embedded session topic
db.enrollments.find({ "sessions.topic": "Hello World" })
```

### 4b. Projection — Select Specific Fields

```javascript
// Return only name and email (exclude _id)
db.students.find({}, { full_name: 1, email: 1, _id: 0 })

// Return all except address
db.students.find({}, { address: 0 })

// Return name and first tag only
db.courses.find(
  { tags: { $exists: true } },
  { name: 1, "tags.$": 1 }
)

// Return name and first 2 tags
db.courses.find(
  { tags: { $exists: true } },
  { name: 1, tags: { $slice: 2 } }
)
```

### 4c. Sorting, Pagination

```javascript
// Sort by GPA descending
db.students.find().sort({ gpa: -1 })

// Sort by city ascending then name descending
db.students.find().sort({ "address.city": 1, full_name: -1 })

// Pagination — page 1 (2 per page)
db.students.find().limit(2).skip(0)

// Pagination — page 2
db.students.find().limit(2).skip(2)

// Count all students
db.students.find().count()
```

---

## 5. Query Operators

### 5a. Comparison Operators

```javascript
// Students with GPA greater than 3.5
db.students.find({ gpa: { $gt: 3.5 } })

// Courses with tuition between 1,400,000 and 2,000,000
db.courses.find({
  tuition_fee: { $gte: 1400000, $lte: 2000000 }
})

// Enrollments with grade not equal to 90
db.enrollments.find({ grade: { $ne: 90 } })

// Students from Surabaya or Malang
db.students.find({
  "address.city": { $in: ["Surabaya", "Malang"] }
})

// Students NOT from Java provinces
db.students.find({
  "address.province": { $nin: ["Jawa Timur", "Jawa Barat", "DI Yogyakarta"] }
})
```

### 5b. Logical Operators

```javascript
// Computer science courses AND tuition above 1,500,000
db.courses.find({
  $and: [
    { category: { $eq: "computer_science" } },
    { tuition_fee: { $gt: 1500000 } }
  ]
})

// Courses in computer_science OR mathematics with credits >= 4
db.courses.find({
  $or: [
    { category: "computer_science" },
    { credits: { $gte: 4 } }
  ]
})

// Students with hobbies that do NOT include "Gaming"
db.students.find({
  hobbies: { $not: { $in: ["Gaming"] } }
})
```

### 5c. Element Operators

```javascript
// Students that have an emergency contact field
db.students.find({
  "contact.emergency": { $exists: true }
})

// Students that DON'T have an emergency contact
db.students.find({
  "contact.emergency": { $exists: false }
})

// Verify gpa field type is decimal
db.students.find({
  gpa: { $type: "decimal" }
})

// Courses where tuition_fee is int or long
db.courses.find({
  tuition_fee: { $type: ["int", "long"] }
})
```

### 5d. Evaluation Operators

```javascript
// Courses where tuition_fee is divisible by 500,000
db.courses.find({
  tuition_fee: { $mod: [500000, 0] }
})

// Students where name contains "Rizky" (case insensitive)
db.students.find({
  full_name: { $regex: /rizky/i }
})

// Validate schema — students must have email (string) and gpa (number)
db.students.find({
  $jsonSchema: {
    required: ["email", "gpa"],
    properties: {
      email: { bsonType: "string" },
      gpa:   { bsonType: "decimal" }
    }
  }
})
```

### 5e. Array Operators

```javascript
// Courses tagged with both "fundamental" AND "beginner"
db.courses.find({
  tags: { $all: ["fundamental", "beginner"] }
})

// Students who hobby at least one of: "Coding" or "Gaming"
db.students.find({
  hobbies: { $elemMatch: { $in: ["Coding", "Gaming"] } }
})

// Students with exactly 2 hobbies
db.students.find({
  hobbies: { $size: 2 }
})

// Enrollments with ratings array, return only matching tags
db.courses.find({}, {
  name: 1,
  tags: {
    $elemMatch: { $in: ["database", "algebra"] }
  }
})
```

---

## 6. Update Documents

### 6a. Field Update Operators

```javascript
// Add stock field to all courses
db.courses.updateMany({}, {
  $set: { available_slots: 0 }
})

// Set available_slots based on max_students
db.courses.updateOne(
  { code: "CS101" },
  { $set: { available_slots: 40 } }
)

// Increment available_slots by 5
db.courses.updateMany({}, {
  $inc: { available_slots: 5 }
})

// Rename field available_slots to open_slots
db.courses.updateMany({}, {
  $rename: { available_slots: "open_slots" }
})

// Remove open_slots field
db.courses.updateMany({}, {
  $unset: { open_slots: "" }
})

// Set lastModifiedDate to current date on all enrollments
db.enrollments.updateMany({}, {
  $currentDate: {
    lastModifiedDate: { $type: "date" }
  }
})
```

### 6b. Update with Condition

```javascript
// Update grade for a specific student and course
db.enrollments.updateOne(
  { student_id: "dzaru", course_code: "CS101" },
  { $set: { grade: 92 } }
)

// Set status to "at_risk" for students with GPA below 3.0
db.students.updateMany(
  { gpa: { $lt: 3.0 } },
  { $set: { status: "at_risk" } }
)
```

### 6c. Array Update Operators

```javascript
// Add tag "popular" to CS101 (only if not already there)
db.courses.updateOne(
  { code: "CS101" },
  { $addToSet: { tags: "popular" } }
)

// Add multiple tags at once
db.courses.updateMany({}, {
  $addToSet: {
    tags: { $each: ["trending", "certified"] }
  }
})

// Push a new rating to dzaru's CS101 enrollment
db.enrollments.updateOne(
  { student_id: "dzaru", course_code: "CS101" },
  { $push: { ratings: 95 } }
)

// Push multiple ratings sorted descending, keep last 3
db.enrollments.updateOne(
  { student_id: "dzaru", course_code: "CS301" },
  {
    $push: {
      ratings: {
        $each: [91, 93, 96],
        $sort: -1,
        $slice: 3
      }
    }
  }
)

// Remove ratings below 75 from all enrollments
db.enrollments.updateMany({}, {
  $pull: { ratings: { $lt: 75 } }
})

// Update first matching rating that equals 90
db.enrollments.updateOne(
  { student_id: "dzaru", ratings: 90 },
  { $set: { "ratings.$": 91 } }
)

// Update all ratings in dzaru's CS101 to 100 (for demo)
db.enrollments.updateOne(
  { student_id: "dzaru", course_code: "CS101" },
  { $set: { "ratings.$[]": 100 } }
)

// Update ratings >= 90 to 99 using arrayFilters
db.enrollments.updateMany(
  {},
  { $set: { "ratings.$[elem]": 99 } },
  { arrayFilters: [{ elem: { $gte: 90 } }] }
)

// Remove last element from ratings
db.enrollments.updateOne(
  { student_id: "sariindah" },
  { $pop: { ratings: 1 } }
)
```

### 6d. Replace Entire Document

```javascript
// Replace a course document entirely
db.courses.replaceOne(
  { code: "MATH201" },
  {
    code: "MATH201",
    name: "Linear Algebra & Matrix Theory",
    credits: new NumberInt("3"),
    category: "mathematics",
    tags: ["algebra", "matrix", "intermediate", "updated"],
    max_students: new NumberInt("45"),
    tuition_fee: new NumberLong("1500000")
  }
)
```

---

## 7. Delete Documents

### 7a. Delete One

```javascript
// Insert a test document then delete it
db.students.insertOne({
  _id: "spammer",
  full_name: "Spammer Account"
})

db.students.deleteOne({ _id: "spammer" })
```

### 7b. Delete Many

```javascript
// Insert dummy spam accounts
db.students.insertMany([
  { _id: "spam001", full_name: "Spam 1" },
  { _id: "spam002", full_name: "Spam 2" },
  { _id: "spam003", full_name: "Spam 3" }
])

// Delete all spam accounts using regex
db.students.deleteMany({
  _id: { $regex: /^spam/ }
})

// Verify they are gone
db.students.find({ _id: { $regex: /^spam/ } })
```

> ⚠️ **Warning** — deleted documents cannot be recovered. Always double-check your filter before running `deleteMany`.

---

## 8. Bulk Operations

Perform multiple operations in a single request using `bulkWrite`.

```javascript
db.students.bulkWrite([
  // Insert a new student
  {
    insertOne: {
      document: {
        _id: "andinugraha",
        full_name: "Andi Nugraha Putra",
        email: "andi@example.com",
        gender: "Male",
        gpa: new NumberDecimal("3.60"),
        hobbies: ["Music", "Coding"],
        enrolled_at: new Date("2024-08-01")
      }
    }
  },
  // Insert another student
  {
    insertOne: {
      document: {
        _id: "fitrianingsih",
        full_name: "Fitria Ningsih Putri",
        email: "fitria@example.com",
        gender: "Female",
        gpa: new NumberDecimal("3.78"),
        hobbies: ["Reading", "Cooking"],
        enrolled_at: new Date("2024-08-01")
      }
    }
  },
  // Update both new students to add status
  {
    updateMany: {
      filter: {
        _id: { $in: ["andinugraha", "fitrianingsih"] }
      },
      update: {
        $set: { status: "new_student" }
      }
    }
  },
  // Delete a student (if exists)
  {
    deleteOne: {
      filter: { _id: "spammer" }
    }
  }
])
```

Verify the result:

```javascript
db.students.find({ _id: { $in: ["andinugraha", "fitrianingsih"] } })
```

---

## 9. Indexes

### 9a. Single Field Index

```javascript
// Create index on GPA for fast sorting/filtering
db.students.createIndex({ gpa: -1 })

// View all indexes
db.students.getIndexes()

// Query using the index
db.students.find({ gpa: { $gt: 3.5 } })

// Verify index is used
db.students.find({ gpa: { $gt: 3.5 } }).explain()
```

### 9b. Compound Index

```javascript
// Index for queries filtering by category and sorting by tuition_fee
db.courses.createIndex({ category: 1, tuition_fee: 1 })

// These queries use the index
db.courses.find({ category: "computer_science", tuition_fee: { $lte: 2000000 } })
db.courses.find({ category: "mathematics" })

// This query does NOT use the compound index efficiently
db.courses.find({ tuition_fee: { $lte: 2000000 } })
```

> 💡 Compound index `(a, b)` supports queries on `a` and `a + b` — but NOT queries on `b` alone.

### 9c. Text Index

```javascript
// Create text index with weights on courses
db.courses.createIndex(
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

// Search for "database"
db.courses.find({ $text: { $search: "database" } })

// Search for "programming" or "algebra"
db.courses.find({ $text: { $search: "programming algebra" } })

// Search exact phrase "Data Structures"
db.courses.find({ $text: { $search: '"Data Structures"' } })

// Search "calculus" but exclude "linear"
db.courses.find({ $text: { $search: "calculus -linear" } })

// Return with relevance score
db.courses.find(
  { $text: { $search: "fundamental" } },
  { searchScore: { $meta: "textScore" } }
)
```

### 9d. Wildcard Index

```javascript
// Students might have different custom fields — use wildcard index
db.students.updateMany(
  { _id: { $in: ["dzaru", "sariindah"] } },
  {
    $set: {
      customFields: {
        scholarship: "Beasiswa Unggulan",
        dormitory: "Asrama A"
      }
    }
  }
)

db.students.updateOne(
  { _id: "budiprakoso" },
  {
    $set: {
      customFields: {
        organization: "BEM Fakultas",
        dormitory: "Asrama B"
      }
    }
  }
)

// Create wildcard index for all customFields sub-fields
db.students.createIndex({ "customFields.$**": 1 })

// Query using custom fields
db.students.find({ "customFields.dormitory": "Asrama A" })
db.students.find({ "customFields.scholarship": "Beasiswa Unggulan" })
```

### 9e. Unique Index

```javascript
// Ensure email is unique across all students
db.students.createIndex(
  { email: 1 },
  { unique: true, sparse: true }
)

// This will succeed
db.students.updateOne(
  { _id: "rizkyfathan" },
  { $set: { email: "rizky.fathan@example.com" } }
)

// This will fail — email already exists
db.students.insertOne({
  _id: "duplicate_test",
  email: "dzaru@example.com"
})
```

### 9f. TTL Index

```javascript
// Create sessions collection with TTL — auto-delete after 30 seconds
db.createCollection("sessions")

db.sessions.createIndex(
  { createdAt: 1 },
  { expireAfterSeconds: 30 }
)

// Insert a session
db.sessions.insertOne({
  _id: new ObjectId(),
  student_id: "dzaru",
  token: "abc123xyz",
  createdAt: new Date()
})

// Wait 30+ seconds then verify it's gone
db.sessions.find()
```

> 📌 MongoDB's background TTL process runs every 60 seconds, so actual deletion may take up to 60 seconds after expiry.

### 9g. Partial Index

```javascript
// Only index enrollments with grade > 0 (skip ungraded)
db.enrollments.createIndex(
  { grade: 1 },
  {
    partialFilterExpression: {
      grade: { $gt: 0 }
    }
  }
)

// Query that uses the partial index
db.enrollments.find({ grade: { $eq: 90 }, student_id: "dzaru" })
```

### 9h. Case Insensitive Index

```javascript
// Create case insensitive index on full_name
db.students.createIndex(
  { full_name: 1 },
  {
    collation: { locale: "en", strength: 2 }
  }
)

// Query using collation — matches regardless of case
db.students.find({
  full_name: "DZARU RIZKY FATHAN FORTUNA"
}).collation({ locale: "en", strength: 2 })
```

### 9i. Drop Index

```javascript
// Drop a specific index
db.students.dropIndex({ gpa: -1 })

// View remaining indexes
db.students.getIndexes()
```

---

## 10. Security & User Management

### 10a. Create Admin User

```javascript
use admin

db.createUser({
  user: "campusdb_admin",
  pwd: "campusdb_admin",
  roles: [
    "userAdminAnyDatabase",
    "readWriteAnyDatabase"
  ]
})
```

### 10b. Restart with Authentication

Stop the server (Ctrl+C) then restart with `--auth`:

```bash
bin/mongod --auth --dbpath=~/campusdb-data
```

Connect with credentials:

```bash
bin/mongosh "mongodb://campusdb_admin:campusdb_admin@localhost:27017/campusdb?authSource=admin"
```

### 10c. Create Application Users

```javascript
use campusdb

// Read-only user — for reporting dashboard
db.createUser({
  user: "campusdb_reader",
  pwd: "reader123",
  roles: [
    { role: "read", db: "campusdb" }
  ]
})

// Read-write user — for the main application
db.createUser({
  user: "campusdb_app",
  pwd: "app123",
  roles: [
    { role: "readWrite", db: "campusdb" }
  ]
})
```

### 10d. Create Custom Role

```javascript
use campusdb

// Custom role: can only insert sessions, read everything else
db.createRole({
  role: "session_writer",
  privileges: [
    {
      resource: { db: "campusdb", collection: "sessions" },
      actions: ["insert"]
    }
  ],
  roles: [
    { role: "read", db: "campusdb" }
  ]
})

// Create user with custom role
db.createUser({
  user: "session_service",
  pwd: "session123",
  roles: ["session_writer"]
})

// View roles with their privileges
db.getRoles({ showPrivileges: true })
```

### 10e. Update and Delete Users

```javascript
// Change password
db.changeUserPassword("campusdb_reader", "newreader456")

// Update user roles
db.updateUser("campusdb_app", {
  roles: [
    { role: "readWrite", db: "campusdb" },
    { role: "read", db: "admin" }
  ]
})

// View all users
db.getUsers()

// Delete user
db.dropUser("campusdb_reader")
```

---

## 11. Backup & Restore

Make sure MongoDB Database Tools are installed from `https://www.mongodb.com/try/download/database-tools`.

### 11a. Backup with mongodump (Binary)

```bash
bin/mongodump \
  --host=localhost \
  --port=27017 \
  --username=campusdb_admin \
  --password="campusdb_admin" \
  --authenticationDatabase=admin \
  --db=campusdb \
  --out=backup-dump
```

Verify the backup folder was created:

```bash
ls backup-dump/campusdb/
```

Expected output:

```
courses.bson     enrollments.bson   sessions.bson
courses.metadata.json  enrollments.metadata.json  sessions.metadata.json
students.bson    students.metadata.json
```

### 11b. Export with mongoexport (JSON)

```bash
# Export students collection to JSON
bin/mongoexport \
  --host=localhost \
  --port=27017 \
  --username=campusdb_admin \
  --password="campusdb_admin" \
  --authenticationDatabase=admin \
  --db=campusdb \
  --collection=students \
  --out=students.json
```

### 11c. Restore with mongorestore

```bash
bin/mongorestore \
  --host=localhost \
  --port=27017 \
  --username=campusdb_admin \
  --password="campusdb_admin" \
  --authenticationDatabase=admin \
  --db=campusdb-restore \
  backup-dump/campusdb
```

Verify:

```javascript
use campusdb-restore
db.students.find()
```

### 11d. Import with mongoimport

```bash
bin/mongoimport \
  --host=localhost \
  --port=27017 \
  --username=campusdb_admin \
  --password="campusdb_admin" \
  --authenticationDatabase=admin \
  --db=campusdb-import \
  --collection=students \
  --file=students.json
```

> 💡 Use `mongodump` when your database has binary data or is large. Use `mongoexport` when you need human-readable JSON or want to migrate specific collections.

---

## 🏆 Challenge Tasks

Once you've completed the practice above, try these on your own:

---

### Challenge 1 — Query & Projection

Find all students from **Jawa Timur** province who have a GPA above 3.5, sorted by GPA descending. Return only `full_name`, `email`, `gpa`, and `address.city` — exclude `_id`.

---

### Challenge 2 — Array & Logical Operators

Find all courses that have the tag `"fundamental"` AND a tuition fee below 1,500,000. Then also find courses that have `"intermediate"` OR `"advanced"` in their tags.

---

### Challenge 3 — Update & Array Modifiers

For the enrollment of student `budiprakoso` in course `CS201`:
- Push ratings `[82, 85, 87, 90]` using `$each`, sorted descending, keeping only the top 3
- Then update all ratings that are above 85 to 99 using `arrayFilters`

---

### Challenge 4 — Bulk Write

Using a single `bulkWrite` call, do all of these at once:
- Insert a new student (`_id: "testbulk"`, `full_name: "Test Bulk"`, `email: "bulk@example.com"`, `gpa: 3.0`)
- Update all students with GPA below 3.2 to add `status: "probation"`
- Delete the student you just inserted (`_id: "testbulk"`)

---

### Challenge 5 — Indexes

Create a compound index on the `enrollments` collection for `student_id` and `course_code`. Then use `explain()` to verify the index is used when querying `{ student_id: "dzaru", course_code: "CS101" }`.

---

### Challenge 6 — Custom Role

Create a custom role called `enrollment_writer` that can only `insert` and `find` on the `enrollments` collection. Create a user `enrollment_service` with this role and verify it cannot update or delete enrollment documents.

---

### Challenge 7 — Backup & Restore

Export the `enrollments` collection to `enrollments.json` using `mongoexport`. Then import it into a new database called `campusdb-backup` using `mongoimport` and verify the document count matches the original.

---

## ✅ Concepts Covered

| Concept | Where Practiced |
|---|---|
| Start MongoDB & connect shell | Step 1 |
| Create database & collections | Step 2 |
| insertOne | Step 3a |
| insertMany | Step 3b, 3c |
| Embedded documents | Step 3d — sessions inside enrollments |
| Reference pattern | Step 3d — student_id referencing students |
| ObjectId & BSON types | Step 3b, 3d |
| find() & projection | Step 4a, 4b |
| sort(), limit(), skip(), count() | Step 4c |
| Comparison operators ($eq, $gt, $in, $nin) | Step 5a |
| Logical operators ($and, $or, $not) | Step 5b |
| Element operators ($exists, $type) | Step 5c |
| Evaluation operators ($regex, $mod, $jsonSchema) | Step 5d |
| Array operators ($all, $elemMatch, $size) | Step 5e |
| $set, $unset, $rename, $inc, $currentDate | Step 6a |
| Conditional update | Step 6b |
| $addToSet, $push, $pull, $pop | Step 6c |
| $each, $sort, $slice, arrayFilters | Step 6c |
| replaceOne | Step 6d |
| deleteOne & deleteMany | Step 7 |
| bulkWrite | Step 8 |
| Single field index | Step 9a |
| Compound index | Step 9b |
| Text index with weights | Step 9c |
| Wildcard index | Step 9d |
| Unique index | Step 9e |
| TTL index | Step 9f |
| Partial index | Step 9g |
| Case insensitive index | Step 9h |
| dropIndex | Step 9i |
| Create admin user | Step 10a |
| Enable --auth | Step 10b |
| Create app users with roles | Step 10c |
| Custom roles & privileges | Step 10d |
| Update & delete users | Step 10e |
| mongodump & mongorestore | Step 11a, 11c |
| mongoexport & mongoimport | Step 11b, 11d |
