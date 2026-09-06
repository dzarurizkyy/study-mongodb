# Study MongoDB 🍃

A comprehensive MongoDB reference guide covering core fundamentals, data modeling, CRUD, indexing, access control, and backup & restore tools.

## Installation 🔧

1. **Download MongoDB**:
   - Visit `https://www.mongodb.com/try/download/community`
   - Download the archive file for your operating system
   - Extract the downloaded file

2. **Run the MongoDB Server**:

   ```bash
   # Create data directory
   mkdir -p /path/to/data

   # Start MongoDB Server
   bin/mongod --dbpath=/path/to/data
   ```

3. **Install a MongoDB Client** (choose one):

   `MongoDB Compass (GUI)`
   - Download at `https://www.mongodb.com/products/tools/compass`
   - Default connection: `mongodb://localhost:27017`

   `MongoDB Shell (CLI)`
   - Download at `https://www.mongodb.com/products/tools/shell`
   - Run: `bin/mongosh mongodb://localhost:27017`

## List of Material 📚

- 🍃 **[MongoDB Basics](001-mongodb-basics.md)**

   Hands-on mongosh guide covering data modeling, full document lifecycle (CRUD & bulk writes), indexing, security, and backup/restore.

  ```javascript
  // Index the fields the query filters and sorts on
  db.products.createIndex({ stock: 1, tags: 1 })

  db.products.find(
    {
      stock: { $gt: 0 },
      tags: { $all: ["samsung", "monitor"] }
    },
    { name: 1, price: 1, _id: 0 }
  ).sort({ price: -1 }).limit(5)
  ```

  Run commands in MongoDB Shell:

  ```bash
  # Connect to MongoDB
  bin/mongosh mongodb://localhost:27017

  # Enable authentication
  bin/mongod --auth --dbpath=/path/to/data

  # Backup and restore a database
  bin/mongodump --db=study --out=backup-dump
  bin/mongorestore --db=study-restore backup-dump/study
  ```

## 📍 References

- [Udemy](https://www.udemy.com/course/belajar-mongodb/?couponCode=KEEPLEARNING)

## 👨‍💻 Contributors

- [Dzaru Rizky Fathan Fortuna](https://www.linkedin.com/in/dzarurizky)
