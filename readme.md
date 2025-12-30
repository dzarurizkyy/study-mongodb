# Study MongoDB 🍃
This repository contains a practical guide to learn MongoDB from installation to advanced operations, with hands-on scenarios to practice and strengthen your MongoDB skills.

## Installation 🔧

1. **Download MongoDB**:
   - Visit `https://www.mongodb.com/try/download/community`
   - Download the archive file for your operating system
   - Extract the downloaded file

2. **Setup MongoDB**:
   ```bash
   # Create data directory
   mkdir -p /path/to/data
   
   # Start MongoDB Server
   bin/mongod --dbpath=/path/to/data
   ```

3. **Install MongoDB Client** (Choose one):
   
   `MongoDB Compass (GUI)`
   - Download at `https://www.mongodb.com/products/tools/compass`
   - Default connection: `mongodb://localhost:27017`
   
   `MongoDB Shell (CLI)`
   - Download at `https://www.mongodb.com/products/tools/shell`
   - Run: `bin/mongosh mongodb://localhost:27017`

## List of Material 📚

* 📘 **MongoDB Basic**
  
    Contains comprehensive MongoDB operations including database management, CRUD operations, query operators, indexing, data modeling, security, backup/restore, and bulk operations.
     
     ```javascript
     // Example: Complete workflow
     
     // 1. Setup database and collection
     use shop_db;
     db.createCollection("products")
     
     // 2. Insert documents
     db.products.insertMany([
       { _id: 1, name: "Laptop ASUS", category: "electronics", price: 15000000, stock: 10, tags: ["laptop", "asus"] },
       { _id: 2, name: "Samsung S23", category: "electronics", price: 12000000, stock: 5, tags: ["phone", "samsung"] },
       { _id: 3, name: "Indomie Goreng", category: "food", price: 3000, stock: 100, tags: ["food", "instant"] }
     ]);
     
     // 3. Query with operators
     db.products.find({
       price: { $gt: 1000000 },
       category: "electronics"
     })
     
     // 4. Update documents
     db.products.updateOne(
       { name: "Indomie Goreng" },
       { 
         $set: { price: 3500 },
         $addToSet: { tags: "popular" }
       }
     )
     
     // 5. Create index for performance
     db.products.createIndex({ category: 1 })
     
     // 6. Bulk operations
     db.products.bulkWrite([
       {
         insertOne: {
           document: { _id: 4, name: "Keyboard Mechanical", category: "electronics" }
         }
       },
       {
         updateMany: {
           filter: { category: "food" },
           update: { $set: { stock: 200 } }
         }
       },
       {
         deleteOne: { filter: { _id: 2 } }
       }
     ])
     ```
     
     Run commands in MongoDB Shell:
     ```bash
     # Connect to MongoDB
     bin/mongosh mongodb://localhost:27017
     
     # Enable authentication
     bin/mongod --auth --dbpath=/path/to/data
     
     # Backup database
     bin/mongodump --db=shop_db --out=backup-dump
     
     # Restore database
     bin/mongorestore --db=shop_db_restored backup-dump/shop_db
     ```

## 📍 References

* [Udemy](https://www.udemy.com/course/belajar-mongodb/?couponCode=KEEPLEARNING)

## 👨‍💻 Contributors

* [Dzaru Rizky Fathan Fortuna](https://www.linkedin.com/in/dzarurizky)
