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
