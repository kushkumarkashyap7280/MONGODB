# Day 2: MongoDB CRUD Operations

## 📌 Overview
This document covers the basic CRUD (Create, Read, Update, Delete) operations in MongoDB using the MongoDB Shell (mongosh).

## 🔄 Connecting to MongoDB
```bash
mongosh
use students  # Switch to 'students' database
```

## ➕ Create Operations

### Insert a Single Document
```javascript
db.data.insertOne({
  'name': 'kush kumar',
  'age': 35,
  'course': 'software development'
})
```

### Insert Multiple Documents
```javascript
db.data.insertMany([
  {'name': 'kush kumar', 'age': 35, 'course': 'software development'},
  {'name': 'sakshi gautam', 'age': 35, 'course': 'software development'}
])
```

> **Note**: `insertMany()` requires an array of documents. Forgetting the square brackets `[]` will result in an error.

## 🔍 Read Operations

### Find All Documents
```javascript
db.data.find()
```

### Find a Single Document
```javascript
// Returns the first matching document or null
db.data.findOne({ 'name': 'kush kumar' })
// Returns:
// {
//   _id: ObjectId('689d7c5fcdaa9e3fb5eec4a9'),
//   name: 'kush kumar',
//   age: 35,
//   course: 'software development'
// }
```

> **Note**: `findOne()` is called on the collection, not the database object. 
> - ❌ Incorrect: `db.findOne()`
> - ✅ Correct: `db.collection.findOne()`

### Find with Conditions
```javascript
// Find by exact field match
db.data.find({ 'name': 'kush kumar' })

// Find with comparison operators
db.data.find({ 'age': { $gt: 30 } })  // Greater than 30
db.data.find({ 'age': { $lt: 40 } })  // Less than 40
db.data.find({ 'age': { $ne: 35 } })  // Not equal to 35

// Find by multiple conditions (AND)
db.data.find({
  'course': 'software development',
  'age': 35
})

// Find with OR condition
db.data.find({
  $or: [
    { 'name': 'kush kumar' },
    { 'name': 'sakshi gautam' }
  ]
})
```

### Projection: Selecting Specific Fields
```javascript
// Return only name and age fields
db.data.find(
  { 'course': 'software development' },
  { 'name': 1, 'age': 1, '_id': 0 }
)
```

## ✏️ Update Operations

### Update a Single Document
```javascript
db.data.updateOne(
  { 'name': 'kush kumar' },
  { $set: { 'age': 36 } }
)
```

### Update Multiple Documents
```javascript
db.data.updateMany(
  { 'course': 'software development' },
  { $set: { 'status': 'active' } }
)
```

## ❌ Delete Operations

### Delete a Single Document
```javascript
db.data.deleteOne({ 'name': 'kush kumar' })
```

### Delete Multiple Documents
```javascript
db.data.deleteMany({ 'course': 'software development' })
```

## 🎯 Common Mistakes
1. **Missing Quotes**: 
   ```javascript
   // Incorrect
   db.data.insertOne({name: kush kumar, age: 35})
   
   // Correct
   db.data.insertOne({'name': 'kush kumar', 'age': 35})
   ```

2. **Missing Array in insertMany**:
   ```javascript
   // Incorrect
   db.data.insertMany({...}, {...})
   
   // Correct
   db.data.insertMany([{...}, {...}])
   ```

## 🔄 Bulk Write Operations

### Ordered vs Unordered Operations
By default, `insertMany()` performs an ordered insert. If an error occurs, MongoDB stops processing the remaining operations.

#### Ordered Insert (Default)
```javascript
db.data.insertMany([
  { _id: 1, name: 'John' },
  { _id: 1, name: 'Doe' },  // Duplicate _id will cause error
  { _id: 2, name: 'Jane' }   // This won't be processed
])
```

#### Unordered Insert (Continues on Error)
```javascript
db.data.insertMany([
  { _id: 1, name: 'John' },
  { _id: 1, name: 'Doe' },  // Duplicate _id will be skipped
  { _id: 2, name: 'Jane' }   // This will still be processed
], { ordered: false })
```

## 🚨 Error Handling

### Handling Duplicate Key Errors
When inserting documents with existing `_id` values:

```javascript
try {
  db.data.insertMany([
    { _id: ObjectId('689d7d1ecdaa9e3fb5eec4ac'), name: 'lovely' },
    { name: 5 },
    { name: 'kumar' }
  ], { ordered: false });
} catch (e) {
  if (e.code === 11000) {
    print('Duplicate key error occurred');
    print('Successfully inserted: ' + e.result.insertedCount);
  } else {
    print('An error occurred: ' + e);
  }
}
```

### Understanding Error Response
When a bulk operation encounters an error:
```json
{
  "insertedCount": 2,
  "insertedIds": {
    "0": ObjectId("689e097e4b426ca556eec4ad"),
    "1": ObjectId("689e097e4b426ca556eec4ae")
  },
  "writeErrors": [
    {
      "index": 2,
      "code": 11000,
      "errmsg": "E11000 duplicate key error collection: students.data index: _id_ dup key: { _id: ObjectId('689d7d1ecdaa9e3fb5eec4ac') }"
    }
  ]
}
```

## 📝 Best Practices
- Always use quotes around field names for consistency
- Use meaningful collection names (plural, lowercase)
- Include error handling in your operations
- Use `$set` operator when updating specific fields
- Consider using `ordered: false` for bulk inserts when you want to continue on error
- Always check the result object for successful operations and errors

## 🔍 Next Steps
- Learn about query operators (`$in`, `$gt`, `$lt`, etc.)
- Explore indexing for better query performance
- Learn about aggregation framework

## 📚 Resources
- [MongoDB CRUD Operations](https://www.mongodb.com/docs/manual/crud/)
- [MongoDB Query Operators](https://www.mongodb.com/docs/manual/reference/operator/query/)
- [MongoDB Update Operators](https://www.mongodb.com/docs/manual/reference/operator/update/)
