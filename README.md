# 📚 Complete MongoDB Cheat Sheet
*Your Ultimate Reference Guide for MongoDB Operations*

---

## 🚀 Table of Contents
1. [Getting Started](#getting-started)
2. [Basic Terminology](#basic-terminology)
3. [Connection & Setup](#connection--setup)
4. [Basic Commands](#basic-commands)
5. [CRUD Operations](#crud-operations)
6. [Advanced Querying](#advanced-querying)
7. [Update Operations](#update-operations)
8. [Aggregation Pipeline](#aggregation-pipeline)
9. [Indexing](#indexing)
10. [Database Administration](#database-administration)
11. [Best Practices](#best-practices)
12. [Common Patterns](#common-patterns)

---

## 🎯 Getting Started

### Installation Requirements
- **MongoDB Community Server**: [Download Here](https://docs.mongodb.com/manual/installation)
- **MongoDB Shell (mongosh)**: [Download Here](https://docs.mongodb.com/mongodb-shell/install)
- **MongoDB Compass** (GUI): [Download Here](https://www.mongodb.com/products/compass)



---

## 📖 Basic Terminology

| Term | Description | SQL Equivalent |
|------|-------------|----------------|
| **Database** | Container for collections | Database |
| **Collection** | Group of documents | Table |
| **Document** | Individual record (JSON-like) | Row |
| **Field** | Key-value pair in document | Column |
| **_id** | Unique identifier (auto-generated) | Primary Key |

### Document Structure Example
```javascript
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "John Doe",
  "age": 25,
  "email": "john@example.com",
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "zipCode": "10001"
  },
  "hobbies": ["reading", "swimming", "coding"],
  "isActive": true,
  "createdAt": ISODate("2023-01-15T10:30:00Z")
}
```

---

## 🔌 Connection & Setup

### Basic Connection Commands
```javascript
// Show current database
db

// List all databases
show dbs

// Switch to database (creates if doesn't exist)
use myDatabase

// Show collections in current database
show collections

// Clear screen
cls

// Exit mongosh
exit
```

### Database Operations
```javascript
// Create database (implicitly when inserting data)
use newDatabase
db.users.insertOne({name: "First User"})

// Drop current database
db.dropDatabase()

// Get database stats
db.stats()
```

---

## ⚡ Basic Commands

### Helper Commands
```javascript
// Get help
help

// Collection help
db.collection.help()

// Show database methods
db.help()

```

---

## 📝 CRUD Operations

### 🟢 CREATE Operations

#### Insert Single Document
```javascript
// Basic insert
db.users.insertOne({
  name: "Alice Johnson",
  age: 28,
  email: "alice@example.com"
})



```

#### Insert Multiple Documents
```javascript
// Ordered insert (stops on first error)
db.users.insertMany([
  {name: "David", age: 30},
  {name: "Eve", age: 25},
  {name: "Frank", age: 35}
])

// Unordered insert (continues despite errors)
db.users.insertMany([
  {name: "Grace", age: 22},
  {name: "Henry", age: 28}
], {ordered: false})

// Insert with write concern
db.users.insertOne(
  {name: "Ivy", age: 24},
  {writeConcern: {w: "majority", wtimeout: 5000}}
)
```

### 🔍 READ Operations

#### Basic Find Operations
```javascript
// Find all documents
db.users.find()

// Find one document
db.users.findOne()

// Find with filter
db.users.find({name: "Alice Johnson"})

// Find with multiple conditions (AND)
db.users.find({age: 28, department: "Engineering"})
```

#### Field Projection
```javascript
// Include specific fields (1 = include, 0 = exclude)
db.users.find({}, {name: 1, email: 1})

// Exclude specific fields
db.users.find({}, {password: 0, _id: 0})

// Include all except specified
db.users.find({age: {$gte: 25}}, {createdAt: 0})
```

#### Sorting, Limiting, and Skipping
```javascript
// Sort ascending (1) and descending (-1)
db.users.find().sort({age: 1, name: -1})

// Limit results
db.users.find().limit(5)

// Skip documents (useful for pagination)
db.users.find().skip(10).limit(5)

```

#### Counting Documents
```javascript
// Count all documents
db.users.countDocuments()

// Count with filter
db.users.countDocuments({age: {$gte: 18}})

// Estimated count (faster, less accurate)
db.users.estimatedDocumentCount()
```

### 🔄 UPDATE Operations

#### Update Single Document
```javascript
// Update one document
db.users.updateOne(
  {name: "Alice Johnson"},
  {$set: {age: 29, lastModified: new Date()}}
)

// Update with upsert (insert if not found)
db.users.updateOne(
  {email: "new@example.com"},
  {$set: {name: "New User", age: 25}},
  {upsert: true}
)
```

#### Update Multiple Documents
```javascript
// Update all matching documents
db.users.updateMany(
  {department: "Engineering"},
  {$set: {bonus: 1000}}
)

// Update all documents
db.users.updateMany(
  {},
  {$set: {lastUpdated: new Date()}}
)
```

#### Replace Document
```javascript
// Replace entire document (except _id)
db.users.replaceOne(
  {name: "Old Name"},
  {
    name: "New Name",
    age: 30,
    email: "new@example.com",
    department: "Sales"
  }
)
```

#### Find and Modify
```javascript
// Find and update (returns original document by default)
db.users.findOneAndUpdate(
  {name: "Alice"},
  {$inc: {age: 1}},
  {returnNewDocument: true}
)

// Find and replace
db.users.findOneAndReplace(
  {_id: ObjectId("...")},
  {name: "Replaced User", age: 25}
)
```

### 🗑️ DELETE Operations

```javascript
// Delete one document
db.users.deleteOne({name: "Alice Johnson"})

// Delete multiple documents
db.users.deleteMany({age: {$lt: 18}})

// Delete all documents (keeps collection)
db.users.deleteMany({})

// Find and delete
db.users.findOneAndDelete({name: "Bob Smith"})

// Drop entire collection
db.users.drop()
```

---

## 🔍 Advanced Querying

### Comparison Operators
```javascript
// Equal
db.users.find({age: {$eq: 25}})

// Not equal
db.users.find({age: {$ne: 25}})

// Greater than / Greater than or equal
db.users.find({age: {$gt: 18}})
db.users.find({age: {$gte: 18}})

// Less than / Less than or equal
db.users.find({age: {$lt: 65}})
db.users.find({age: {$lte: 65}})

// In array
db.users.find({age: {$in: [25, 30, 35]}})

// Not in array
db.users.find({age: {$nin: [25, 30, 35]}})
```

### Logical Operators
```javascript
// AND (implicit with multiple fields)
db.users.find({age: {$gte: 18}, department: "Engineering"})

// AND (explicit)
db.users.find({
  $and: [
    {age: {$gte: 18}},
    {department: "Engineering"}
  ]
})

// OR
db.users.find({
  $or: [
    {age: {$lt: 18}},
    {age: {$gt: 65}}
  ]
})

// NOT
db.users.find({
  age: {$not: {$eq: 25}}
})

// NOR (not or)
db.users.find({
  $nor: [
    {age: {$lt: 18}},
    {department: "HR"}
  ]
})
```


##  Update Operations (Advanced)

### Field Update Operators
```javascript
// Set field value
db.users.updateOne({_id: 1}, {$set: {name: "New Name"}})

// Unset (remove) field
db.users.updateOne({_id: 1}, {$unset: {temporaryField: ""}})

// Increment/decrement number
db.users.updateOne({_id: 1}, {$inc: {age: 1, score: -10}})

// Multiply field
db.users.updateOne({_id: 1}, {$mul: {price: 1.1}})

// Rename field
db.users.updateOne({_id: 1}, {$rename: {oldName: "newName"}})

// Set minimum value
db.users.updateOne({_id: 1}, {$min: {lowScore: 50}})

// Set maximum value
db.users.updateOne({_id: 1}, {$max: {highScore: 100}})

// Set current date
db.users.updateOne({_id: 1}, {$currentDate: {lastModified: true}})
```

### Array Update Operators
```javascript
// Add element to array
db.users.updateOne({_id: 1}, {$push: {hobbies: "cooking"}})

// Add multiple elements
db.users.updateOne({_id: 1}, {
  $push: {
    hobbies: {
      $each: ["painting", "dancing"],
      $sort: 1
    }
  }
})

// Add to set (no duplicates)
db.users.updateOne({_id: 1}, {$addToSet: {tags: "important"}})

// Remove element from array
db.users.updateOne({_id: 1}, {$pull: {hobbies: "cooking"}})

// Remove multiple elements
db.users.updateOne({_id: 1}, {$pullAll: {scores: [0, 5]}})

// Remove first/last element
db.users.updateOne({_id: 1}, {$pop: {hobbies: 1}})  // last
db.users.updateOne({_id: 1}, {$pop: {hobbies: -1}}) // first

// Update array element by position
db.users.updateOne(
  {_id: 1},
  {$set: {"hobbies.0": "updated hobby"}}
)

// Update array element by condition
db.users.updateOne(
  {_id: 1, "scores.subject": "math"},
  {$set: {"scores.$.score": 95}}
)

// Update all array elements
db.users.updateMany(
  {},
  {$inc: {"scores.$[].score": 5}}
)

// Update array elements with condition
db.users.updateMany(
  {},
  {$set: {"scores.$[elem].grade": "A"}},
  {arrayFilters: [{"elem.score": {$gte: 90}}]}
)
```


## Link to read more 



 [From MongoDB](https://www.mongodb.com/developer/products/mongodb/cheat-sheet/?msockid=12b83ac2fb366d0214fd2f3dfa246cdb)
 [MongoDB Ultimate Guide](https://blog.webdevsimplified.com/2022-02/mongo-db/)
 
 