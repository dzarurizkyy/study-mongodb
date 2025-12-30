# MongoDB - Hands-on Practice

A comprehensive guide to MongoDB operations covering CRUD, querying, indexing, data modeling, and security basics.

## 📋 Prerequisites

- MongoDB installed and running
- MongoDB Shell (mongosh) or MongoDB Compass
- Basic understanding of NoSQL databases

## 🚀 Getting Started

- **Setup Database & Collection**

   Create a new database named `shop_db`:
   
   ```javascript
   use shop_db;
   ```
   
   Create a collection named `products` explicitly:
   
   ```javascript
   db.createCollection("products")
   ```
   
   Display all collections to verify:
   
   ```javascript
   db.getCollectionNames()
   ```

   > **Note:** A new database will only appear in the database list after it contains data.

## 📝 CRUD Operations

- **Insert Documents**

   Insert sample product data into the `products` collection:
   
   ```javascript
   db.products.insertMany([
     { _id: 1, name: "Laptop ASUS", category: "electronics", price: 15000000, stock: 10, tags: ["laptop", "asus", "gaming"] },
     { _id: 2, name: "Samsung S23", category: "electronics", price: 12000000, stock: 5, tags: ["handphone", "samsung"] },
     { _id: 3, name: "Indomie Goreng", category: "food", price: 3000, stock: 100, tags: ["food", "instant"] },
     { _id: 4, name: "Logitech Mouse", category: "electronics", price: 500000, stock: 20, tags: ["accessories", "logitech"] }
   ]);
   ```

- **Query Documents**

   Find electronics products with price above 1,000,000:
   
   ```javascript
   db.products.find({
     price: { $gt: 1000000 },
     category: "electronics"
   })
   ```
   
   Find products with tag "food" OR "accessories":
   
   ```javascript
   db.products.find({
     tags: { $in: ["food", "accessories"] }
   })
   ```
   
   Find products containing "ASUS" in the name (using Regex):
   
   ```javascript
   db.products.find({
     name: { $regex: /ASUS/, $options: "i" }
   })
   ```

- **Update Documents**

   Add a new field `discount: 0` to all electronics products:
   
   ```javascript
   db.products.updateMany(
     { category: "electronics" }, 
     { $set: { discount: 0 } }
   )
   ```
   
   Increase Indomie Goreng price to 3500 and add "popular" tag:
   
   ```javascript
   db.products.updateOne(
     { name: { $eq: "Indomie Goreng" } },
     { 
       $set: { price: 3500 },
       $addToSet: { tags: "popular" }
     }
   )
   ```
   
   Decrease Logitech Mouse stock by 2:
   
   ```javascript
   db.products.updateOne(
     { name: "Logitech Mouse" },
     { $inc: { stock: -2 } }
   )
   ```

## 🔍 Advanced Querying (Array & Projection)

- **Array Operations**

   Find products with exactly 2 tags:
   
   ```javascript
   db.products.find({ tags: { $size: 2 } })
   ```

- **Projection**

   Display only `name` and `price` fields (hide `_id`):
   
   ```javascript
   db.products.find({}, {
     name: 1,
     price: 1,
     _id: 0
   })
   ```

- **Sorting**

   Sort products by price (lowest to highest):
   
   ```javascript
   db.products.find({}).sort({ price: 1 })
   ```

## ⚡ Indexing & Performance

- **Before Indexing**

   Check query performance without index:
   
   ```javascript
   db.products.find({ category: "food" }).explain("executionStats")
   ```

   Look for the `totalDocsExamined` field in the output.

- **Create Index**

   Create an index on the `category` field:
   
   ```javascript
   db.products.createIndex({ category: 1 })
   ```

- **After Indexing**

   Run the explain command again:
   
   ```javascript
   db.products.find({ category: "food" }).explain("executionStats")
   ```
   
   Notice the changes:
   - `totalDocsExamined` should be lower
   - Execution stage should show `IXSCAN` instead of `COLLSCAN`

## 🗄️ Data Modeling (Embedding vs Reference)

- **Embedding Pattern**

   Insert an order document with embedded product details:
   
   ```javascript
   db.orders.insertOne({
     _id: new ObjectId(),
     order_date: new Date(),
     customer: {
       name: "Dzaru Rizky Fathan Fortuna",
       email: "andi@example.com"
     },
     items: [
       {
         product_id: 1,
         name: "Laptop ASUS",
         price: 15000000,
         quantity: 1
       },
       {
         product_id: 4,
         name: "Logitech Mouse",
         price: 500000,
         quantity: 2
       }
     ],
     total_payment: 25000000,
     status: "PAID"
   })
   ```

- **Reference Pattern**

   Insert an order document with only product references:
   
   ```javascript
   db.orders_ref.insertOne({
     _id: "ORD-2023-001",
     order_date: new Date(),
     customer_id: "cust_123",
     items: [
       { product_id: 1, quantity: 1 },
       { product_id: 4, quantity: 2 }
     ],
     status: "PENDING"
   })
   ```

## 🔒 Security (Simulation)

- **Switch to Admin Database**

   ```javascript
   use admin
   ```

- **Create User**

   Create a user named `manager_toko` with `readWrite` role for `shop_db`:
   
   ```javascript
   db.createUser({
     user: "manager_toko",
     pwd: "secret",
     roles: [
       { role: "readWrite", db: "shop_db" }
     ]
   })
   ```

- **Verify User Creation**

   Check if the user was created successfully:
   
   ```javascript
   db.getUsers()
   ```

## 🎯 Challenge Case (Bulk Operations)

Use `bulkWrite` to perform 3 operations simultaneously:

```javascript
db.products.bulkWrite([
   // Insert a new product: "Keyboard Mechanical"
  {
    insertOne: {
      document: { _id: 5, name: "Keyboard Mechanical" }
    }
  },
  // Update all food category products to have stock of 200
  {
    updateMany: {
      filter: { category: { $eq: "food" } },
      update: { $set: { stock: 200 } }
    }
  },
  // Delete product with `_id: 2`
  {
    deleteOne: { 
      filter: { _id: 2 } 
    }
  }
])
```

## 📝 Notes

- Always use appropriate indexes for frequently queried fields
- Choose between embedding and referencing based on your data access patterns
- Use bulk operations for better performance when executing multiple operations
- Implement proper security measures in production environments