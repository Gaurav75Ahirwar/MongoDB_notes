# MongoDB Basics

MongoDB is a document-oriented database. It stores data as BSON documents (a binary form of JSON) inside collections. A database contains collections, and a collection contains documents. Documents in the same collection can have different fields.

```text
Database -> Collection -> Document
shop     -> products   -> { name: "Notebook", price: 4.5 }
```

## `mongod`, `mongosh`, and MongoDB Compass

- **`mongod`** is the MongoDB server process. It manages the database and handles connections. It must be running for local clients to connect.
- **`mongosh`** is the MongoDB Shell, a command-line client. Use it to connect to a MongoDB server and run queries and commands.
- **MongoDB Compass** is a graphical client. It lets you browse databases and collections, inspect documents, and run queries without writing every command in a terminal.

In short: `mongod` runs the database; `mongosh` and Compass are two ways to work with it. Both clients can connect to a local server or a remote deployment. For a local server, the common connection address is `mongodb://localhost:27017`.

## CRUD Operations

CRUD means **Create, Read, Update, Delete**. In MongoDB, these operations work with documents in a collection. The examples below use the `products` collection in the `shop` database; run them in `mongosh` after connecting to a server.

```javascript
use shop

// Create
db.products.insertOne({ name: "Notebook", price: 4.5, inStock: true })

// Read
db.products.find({ inStock: true })

// Update
db.products.updateOne(
	{ name: "Notebook" },
	{ $set: { price: 5 } }
)

// Delete
db.products.deleteOne({ name: "Notebook" })
```

`insertOne` adds a document, `find` returns matching documents, `updateOne` changes the first matching document, and `deleteOne` removes the first match. Use a specific filter when updating or deleting so you affect only the intended documents. MongoDB also provides `insertMany`, `findOne`, `updateMany`, and `deleteMany` for working with multiple documents or a single result.

## Connecting to MongoDB and Changing Data Remotely

MongoDB can be accessed from several clients, both locally and remotely:

- **`mongosh`**: connect directly from the terminal.
- **MongoDB Compass**: use a GUI to inspect and edit data visually.
- **Node.js app**: use the official MongoDB Node driver.
- **Django app**: use a MongoDB driver or ODM such as `pymongo` or `MongoEngine`.

### Typical connection strings

```bash
# Local MongoDB
mongodb://localhost:27017

# MongoDB Atlas / remote deployment
mongodb+srv://<username>:<password>@cluster.mongodb.net/<database>?retryWrites=true&w=majority
```

### Connecting in shell

```bash
mongosh "mongodb://localhost:27017/shop"
# or a remote Atlas URI
mongosh "mongodb+srv://<username>:<password>@cluster.mongodb.net/shop"
```

### Important pointers

- Store credentials in environment variables, not directly in source code.
- Use the correct database name and collection name when writing or updating data.
- A remote database is still just MongoDB; the CRUD rules are the same as local MongoDB.
- Restrict DB user permissions to only what the app needs (read/write vs admin).
- In production, use TLS/secure connection strings and avoid hardcoded secrets.

### App-level examples

```javascript
// Node.js / JavaScript app
const { MongoClient } = require("mongodb");

const client = new MongoClient(process.env.MONGO_URI);
client.connect();
const db = client.db("shop");
db.collection("products").insertOne({ name: "Notebook", price: 5 });
```

```python
# Django / Python app
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017")
db = client["shop"]
db["products"].insert_one({"name": "Notebook", "price": 5})
```

MongoDB Compass can connect using the same URI and lets you create, edit, and delete documents visually without writing shell commands.

## Inserting Data Using MongoDB Shell

The shell is one of the easiest ways to create documents quickly.

```javascript
use shop

// Insert one document
 db.products.insertOne({
  name: "Notebook",
  price: 5,
  inStock: true,
  category: "stationery"
})

// Insert many documents
 db.products.insertMany([
  { name: "Pen", price: 2, inStock: true },
  { name: "Eraser", price: 1, inStock: false },
  { name: "Marker", price: 3, inStock: true }
])
```

### `insertOne` vs `insertMany`

- **`insertOne()`** inserts exactly one document.
- **`insertMany()`** inserts multiple documents in one call and is more efficient for bulk data entry.
- Both create documents in a collection and automatically assign an `_id` if one is not provided.
- MongoDB documents do not need a fixed schema, so different documents in the same collection may have different fields.

### `insert` vs `insertMany`

```javascript
// Older shell method
db.products.insert({ name: "Paper", price: 3 });
```

- `insert` is a legacy shell method and is generally not preferred in modern code.
- `insertOne` is clearer and more explicit for a single document.
- `insertMany` is the right choice for multiple documents.
- In modern MongoDB usage, prefer `insertOne` and `insertMany` over `insert`.

### Important insertion pointers

- If no `_id` is specified, MongoDB creates one automatically.
- Use meaningful field names and avoid mixing inconsistent shapes unnecessarily.
- For large batches, `insertMany` is faster and cleaner than inserting one document at a time.
- Validate expected data before insert, especially in production systems.
- Always consider indexing if you will frequently query on fields like `name`, `email`, `category`, or `createdAt`.

This is the core idea behind creating data in MongoDB: you choose a database, a collection, and insert documents in BSON format. The shell, Compass, or app code all work with the same collection and document model.
