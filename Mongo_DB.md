### MongoDB Tutorial: Comprehensive Guide

MongoDB is a NoSQL, document-oriented database that stores data in JSON-like documents, providing flexibility and scalability. Here, we will explore a wide range of MongoDB features from installation to advanced querying and aggregation.

---

### 1. **Installation**
#### a) Local Installation:
- **Windows/Linux/Mac**: You can download MongoDB from [MongoDB's official site](https://www.mongodb.com/try/download/community) and follow the installation instructions for your OS.
- **Docker**: To install MongoDB via Docker:
  ```bash
  docker run -d -p 27017:27017 --name mongodb mongo
  ```
  
#### b) Cloud Installation:
You can use **MongoDB Atlas**, a managed database-as-a-service. Sign up at [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) and create a free cluster.

---

### 2. **Basic Concepts**
MongoDB stores data as **documents** in **collections**. Each document is similar to a JSON object. Collections are similar to tables in relational databases.

- **Database**: Contains collections.
- **Collection**: A group of MongoDB documents.
- **Document**: A set of key-value pairs.

---

### 3. **Basic Operations**

#### a) Connect to MongoDB:
```bash
mongo
```
In Atlas or locally:
```javascript
use myDatabase
```

#### b) Insert a Document:

>:warning: __Replace `collection` with the name of your collection. E.g. `users`, `products`, ...__
```javascript
db.collection.insertOne({
  name: "John Doe",
  age: 28,
  address: { city: "New York", zip: 10001 }
});
```
For multiple documents:
```javascript
db.collection.insertMany([
  { name: "Jane", age: 30 },
  { name: "Bob", age: 35 }
]);
```

#### c) Query Documents:
- **Find all documents**:
  ```javascript
  db.collection.find()
  ```
- **Find with conditions**:
  ```javascript
  db.collection.find({ age: { $gt: 25 } })
  ```
  - Operators: `$gt` (greater than), `$lt` (less than), `$eq` (equal), `$in`, `$or`, etc.

#### d) Update Documents:
- **Update a document**:
  ```javascript
  db.collection.updateOne({ name: "John Doe" }, { $set: { age: 29 } })
  ```
- **Update multiple documents**:
  ```javascript
  db.collection.updateMany({ age: { $lt: 30 } }, { $set: { status: "young" } })
  ```

#### e) Delete Documents:
- **Delete a single document**:
  ```javascript
  db.collection.deleteOne({ name: "John Doe" })
  ```
- **Delete many documents**:
  ```javascript
  db.collection.deleteMany({ age: { $gt: 60 } })
  ```

---

### 4. **Indexes**
Indexes improve query performance by creating fast lookups.

#### a) Create an Index:
```javascript
db.collection.createIndex({ name: 1 })
```
- `1` for ascending, `-1` for descending.

#### b) View Indexes:
```javascript
db.collection.getIndexes()
```

#### c) Drop an Index:
```javascript
db.collection.dropIndex("name_1")
```

---

### 5. **Aggregation Framework**
Aggregation allows complex queries, transformations, and analytics.

#### a) Aggregation Example:
```javascript
db.collection.aggregate([
  { $match: { age: { $gt: 25 } } },
  { $group: { _id: "$city", total: { $sum: 1 } } }
])
```

#### b) Common Aggregation Operators:
- **$match**: Filters documents.
- **$group**: Groups documents by a specified field.
- **$project**: Reshapes documents by adding, removing, or renaming fields.
- **$sort**: Sorts documents.
- **$limit**: Limits the result to a specific number.

---

### 6. **Replication & Sharding**

#### a) Replication:
Replication provides data redundancy and high availability by keeping copies of data on multiple servers.
- **Replica Set**: A group of `mongod` processes that maintain the same dataset.

To configure a replica set:
```bash
mongod --replSet "rs0"
```

#### b) Sharding:
Sharding allows horizontal scaling by partitioning data across multiple servers.
- **Shard Key**: A field that MongoDB uses to distribute documents among shards.

To enable sharding:
```bash
sh.enableSharding("myDatabase")
sh.shardCollection("myDatabase.myCollection", { shardKey: 1 })
```

---

### 7. **Transactions**
Transactions allow you to execute multiple operations atomically.

#### a) Start a Session:
```javascript
const session = db.getMongo().startSession();
session.startTransaction();

try {
  db.collection.insertOne({ name: "Alice" }, { session });
  db.collection.updateOne({ name: "Bob" }, { $set: { age: 40 } }, { session });
  session.commitTransaction();
} catch (e) {
  session.abortTransaction();
} finally {
  session.endSession();
}
```

---

### 8. **Security Best Practices**
#### a) User Authentication:
Ensure that only authenticated users can access the database:
```bash
use admin
db.createUser({
  user: "admin",
  pwd: "password",
  roles: [ { role: "root", db: "admin" } ]
});
```

#### b) Role-Based Access Control (RBAC):
Define roles to restrict access:
```bash
db.createRole({
  role: "readWriteRole",
  privileges: [{ resource: { db: "myDatabase", collection: "" }, actions: ["find", "insert", "update", "remove"] }],
  roles: []
});
```

#### c) Enable TLS/SSL:
For encrypted communication, configure MongoDB with SSL.

---

### 9. **Backup and Restore**
#### a) Backup:
To take a backup using `mongodump`:
```bash
mongodump --db myDatabase --out /backup/location
```

#### b) Restore:
To restore a backup using `mongorestore`:
```bash
mongorestore /backup/location
```

---

### 10. **Monitoring & Performance Tuning**
#### a) Monitoring:
Use **MongoDB Atlas** built-in monitoring or `mongostat` and `mongotop` for local deployments.
```bash
mongostat
mongotop
```

#### b) Performance Tuning Tips:
- **Optimize Indexes**: Ensure queries use appropriate indexes.
- **Sharding**: Distribute large collections across shards.
- **Memory**: Allocate sufficient memory for frequently accessed data.

---

### 11. **MongoDB with Programming Languages**
You can interact with MongoDB through various drivers (e.g., Node.js, Python, Java).

#### a) Node.js Example:
```javascript
const { MongoClient } = require('mongodb');
const client = new MongoClient("mongodb://localhost:27017");

async function run() {
  try {
    await client.connect();
    const database = client.db("myDatabase");
    const collection = database.collection("myCollection");
    const data = await collection.findOne({ name: "Alice" });
    console.log(data);
  } finally {
    await client.close();
  }
}

run().catch(console.dir);
```

#### b) Python Example:
```python
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017/")
db = client.myDatabase
collection = db.myCollection
document = collection.find_one({"name": "Alice"})
print(document)
```

---

### 12. **Schema Design Best Practices**
Although MongoDB is schema-less, designing an efficient schema is essential for performance:
- **Embed documents** if you query both parent and child documents frequently.
- **Reference documents** if relationships are complex, and you rarely fetch related documents together.

---

### 13. **Change Streams**
MongoDB allows real-time tracking of changes to your data using **change streams**.
```javascript
const changeStream = db.collection.watch();
changeStream.on("change", (next) => {
  console.log("Change detected:", next);
});
```

---

### Conclusion
MongoDB provides a flexible and scalable solution for managing large and dynamic datasets. By mastering core concepts like indexing, aggregation, replication, and sharding, you can optimize MongoDB for various use cases. Additionally, leveraging drivers in programming languages allows you to integrate MongoDB into full-stack applications easily.