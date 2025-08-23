Warning: PowerShell detected that you might be using a screen reader and has disabled PSReadLine for compatibility purposes. If you want to re-enable it, run 'Import-Module PSReadLine'.

PS C:\Users\kushk> mongosh
Current Mongosh Log ID: 68a69dd01438848b23eec4a8
Connecting to: mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.6
Using MongoDB: 8.0.12
Using Mongosh: 2.5.6

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/

The server generated these startup warnings when booting
2025-08-21T09:36:06.123+05:30: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted

test> show dbs
admin 40.00 KiB
config 48.00 KiB
kdb 36.00 KiB
local 88.00 KiB
shop 756.00 KiB
test_db 72.00 KiB
user 72.00 KiB
userEnquiry 108.00 KiB
test> use shop
switched to db shop
shop> show collections
categories
comments
products
sales
shop> /// projection

shop> db.comments.find({comments : { $size : 2}})
[
{
\_id: 4,
title: 'Advanced Queries in MongoDB',
content: 'Learn how to write complex queries...',
author: 'Michael Johnson',
comments: [
{ user: 'Grace', text: 'Mind-blowing content!' },
{ user: 'Henry', text: 'Impressive examples.' }
],
metadata: { views: 1500, likes: 60 }
},
{
\_id: 2,
title: 'Deep Dive into Aggregation Framework',
content: 'The aggregation framework in MongoDB...',
author: 'Jane Smith',
comments: [
{ user: 'Charlie', text: 'Very informative!' },
{ user: 'David', text: 'Well explained.' }
],
metadata: { views: 800, likes: 70 }
},
{
\_id: 5,
title: 'Optimizing MongoDB Performance',
content: 'Tips and tricks to improve MongoDB performance...',
author: 'Alex Turner',
comments: [
{ user: 'Ivy', text: 'Very practical advice!' },
{ user: 'Jack', text: 'Helped me a lot.' }
],
metadata: { views: 900, likes: 55 }
},
{
\_id: 3,
title: 'Getting Started with NoSQL Databases',
content: 'NoSQL databases provide flexible data models...',
author: 'Sarah Williams',
comments: [
{ user: 'Eva', text: 'Very helpful!' },
{ user: 'Frank', text: 'Clear explanations.' }
],
metadata: { views: 1200, likes: 40 }
},
{
\_id: 6,
title: 'Schema Design Best Practices',
content: 'Designing schemas for optimal performance...',
author: 'Emily Brown',
comments: [
{ user: 'Kevin', text: 'Invaluable insights!' },
{ user: 'Lily', text: 'Well-structured explanations.' }
],
metadata: { views: 700, likes: 65 }
},
{
\_id: 7,
title: 'Introduction to MongoDB',
content: 'MongoDB is a popular NoSQL database...',
author: 'Vinod Thapa',
comments: [
{ user: 'Alice', text: 'Awesome article!' },
{ user: 'Vinod', text: 'Thanks for sharing.' }
],
metadata: { views: 1000, likes: 70 }
}
]
shop> db.comments.find({comments : { $size : 2}},{comments : 1})
[
{
\_id: 4,
comments: [
{ user: 'Grace', text: 'Mind-blowing content!' },
{ user: 'Henry', text: 'Impressive examples.' }
]
},
{
\_id: 2,
comments: [
{ user: 'Charlie', text: 'Very informative!' },
{ user: 'David', text: 'Well explained.' }
]
},
{
\_id: 5,
comments: [
{ user: 'Ivy', text: 'Very practical advice!' },
{ user: 'Jack', text: 'Helped me a lot.' }
]
},
{
\_id: 3,
comments: [
{ user: 'Eva', text: 'Very helpful!' },
{ user: 'Frank', text: 'Clear explanations.' }
]
},
{
\_id: 6,
comments: [
{ user: 'Kevin', text: 'Invaluable insights!' },
{ user: 'Lily', text: 'Well-structured explanations.' }
]
},
{
\_id: 7,
comments: [
{ user: 'Alice', text: 'Awesome article!' },
{ user: 'Vinod', text: 'Thanks for sharing.' }
]
}
]
shop> db.comments.find({comments : { $size : 2}},{comments : 1 , \_id : 0})
[
{
comments: [
{ user: 'Grace', text: 'Mind-blowing content!' },
{ user: 'Henry', text: 'Impressive examples.' }
]
},
{
comments: [
{ user: 'Charlie', text: 'Very informative!' },
{ user: 'David', text: 'Well explained.' }
]
},
{
comments: [
{ user: 'Ivy', text: 'Very practical advice!' },
{ user: 'Jack', text: 'Helped me a lot.' }
]
},
{
comments: [
{ user: 'Eva', text: 'Very helpful!' },
{ user: 'Frank', text: 'Clear explanations.' }
]
},
{
comments: [
{ user: 'Kevin', text: 'Invaluable insights!' },
{ user: 'Lily', text: 'Well-structured explanations.' }
]
},
{
comments: [
{ user: 'Alice', text: 'Awesome article!' },
{ user: 'Vinod', text: 'Thanks for sharing.' }
]
}
]
shop> db.comments.find({comments : { $size : 2}},{comments : 1, title: 0 , \_id : 0})
MongoServerError[Location31254]: Cannot do exclusion on field title in inclusion projection
shop>

# Day 4: MongoDB Querying and Projections

This session demonstrates querying documents in MongoDB, focusing on array size queries and projection techniques.

## Steps

1. **Connect to MongoDB:**

```shell
mongosh
```

2. **List databases and switch to `shop`:**

```mongodb
show dbs
use shop
show collections
```

3. **Find documents in `comments` where the `comments` array has exactly 2 elements:**

```mongodb
db.comments.find({comments : { $size : 2}})
```

4. **Project only the `comments` field:**

```mongodb
db.comments.find({comments : { $size : 2}}, {comments : 1})
```

5. **Project only the `comments` field, exclude `_id`:**

```mongodb
db.comments.find({comments : { $size : 2}}, {comments : 1, _id : 0})
```

6. **Attempt to exclude `title` while including `comments` (results in error):**

```mongodb
db.comments.find({comments : { $size : 2}}, {comments : 1, title: 0, _id : 0})
```

**Error:**

```
MongoServerError[Location31254]: Cannot do exclusion on field title in inclusion projection
```

## Notes

- In MongoDB projections, you cannot mix inclusion and exclusion (except for `_id`).
- Use `{ field: 1 }` to include fields, `{ field: 0 }` to exclude, but not both in the same projection document (except for `_id`).

---

# Embedded Documents & Array Queries

This section demonstrates querying embedded documents and arrays in MongoDB, including common mistakes and their corrections.

## Examples

### 1. Find documents where a comment is by a specific user

```mongodb
db.comments.find({'comments.user' : "Lily"})
```

**Result:**

```json
[
	{
		_id: 6,
		title: 'Schema Design Best Practices',
		...
		comments: [
			{ user: 'Kevin', text: 'Invaluable insights!' },
			{ user: 'Lily', text: 'Well-structured explanations.' }
		],
		metadata: { views: 700, likes: 65 }
	}
]
```

### 2. Attempt to query multiple conditions on embedded fields (incorrect syntax)

```mongodb
db.comments.find({comments.user : {$eq : "Henry"},comments.metadata.views  : {$gt : 50}})
```

**Error:**

```
SyntaxError: Unexpected token, expected ","
```

**Explanation:**
You cannot use dot notation for multiple levels in a single key. Use the correct field paths for top-level fields only.

### 3. Correct way to query for a user in comments and likes in metadata

```mongodb
db.comments.find({"comments.user" : 'Henry', "metadata.likes" : {$gt : 50} })
```

**Result:**

```json
[
	{
		_id: 4,
		title: 'Advanced Queries in MongoDB',
		...
		comments: [
			{ user: 'Grace', text: 'Mind-blowing content!' },
			{ user: 'Henry', text: 'Impressive examples.' }
		],
		metadata: { views: 1500, likes: 60 }
	}
]
```

### 4. Attempt to use $or incorrectly

```mongodb
db.comments.find({"comments.user" : $or [ 'vinod' , 'Henry'],"metadata.likes" : {$gt : 50} })
```

**Error:**

```
ReferenceError: $or is not defined
```

**Correct usage:**

```mongodb
db.comments.find({
	$or: [
		{ "comments.user": "vinod" },
		{ "comments.user": "Henry" }
	],
	"metadata.likes": { $gt: 50 }
})
```

### 5. Find documents where comments array contains both 'Alice' and 'Vinod'

```mongodb
db.comments.find({"comments.user" : {$all : ['Alice','Vinod']}})
```

**Result:**

```json
[
	{
		_id: 7,
		title: 'Introduction to MongoDB',
		...
		comments: [
			{ user: 'Alice', text: 'Awesome article!' },
			{ user: 'Vinod', text: 'Thanks for sharing.' }
		],
		metadata: { views: 1000, likes: 70 }
	}
]
```

### 6. Find documents with a specific embedded document in comments using $elemMatch

```mongodb
db.comments.find({'comments' : {$elemMatch : { user : "Alice", text : "Awesome article!"}}})
```

**Result:**

```json
[
	{
		_id: 7,
		title: 'Introduction to MongoDB',
		...
		comments: [
			{ user: 'Alice', text: 'Awesome article!' },
			{ user: 'Vinod', text: 'Thanks for sharing.' }
		],
		metadata: { views: 1000, likes: 70 }
	}
]
```

## Notes

- Use dot notation for querying fields inside embedded documents or arrays.
- Use `$or` as a top-level operator, not as a value.
- `$elemMatch` is useful for matching entire embedded documents in arrays.

---

# MongoDB Update Operations

This section demonstrates various update operations in MongoDB, including single document updates, multiple document updates, and common errors to avoid.

## Examples

### 1. View Initial Document

First, let's look at a single product document:

```mongodb
db.products.find().limit(1)
```

**Result:**

```json
[
  {
    _id: ObjectId('64c23601e32f4a51b19b9265'),
    name: 'Kitchen Blender',
    company: '64c23350e32f4a51b19b9233',
    price: 89,
    colors: [ '#ffffff', '#ff9900', '#990000' ],
    image: '/images/product-blender.png',
    category: '64c2342de32f4a51b19b924f',
    isFeatured: true
  }
]
```

### 2. Incorrect Update Syntax

Attempting to update with incorrect ObjectId syntax:

```mongodb
db.products.update({ ObjectId('64c23601e32f4a51b19b9265') }, { $set : {'price' :  45}})
```

**Error:**

```
SyntaxError: Unexpected token
```

### 3. Correct Single Document Update

Using `updateOne` with proper ObjectId syntax:

```mongodb
db.products.updateOne(
  { _id : ObjectId('64c23601e32f4a51b19b9265') },
  { $set : {'price' :  45}}
)
```

**Result:**

```json
{
  "acknowledged": true,
  "insertedId": null,
  "matchedCount": 1,
  "modifiedCount": 1,
  "upsertedCount": 0
}
```

### 4. Update by Field Value

Update a document by matching a field other than \_id:

```mongodb
db.products.updateOne(
  { name : 'Designer Handbag' },
  { $set : {'isFeatured' :  true }}
)
```

### 5. Incorrect Update Operation

Attempting an update without an update operator:

```mongodb
db.products.updateOne({ name : 'Designer Handbag' })
```

**Error:**

```
MongoInvalidArgumentError: Update document requires atomic operators
```

### 6. Bulk Update Operation

Update multiple documents at once:

```mongodb
db.products.updateMany(
  { isFeatured : true },
  { $set : {isFeatured : false}}
)
```

**Result:**

```json
{
  "acknowledged": true,
  "insertedId": null,
  "matchedCount": 85,
  "modifiedCount": 85,
  "upsertedCount": 0
}
```

## Notes

- Always use proper update operators (`$set`, `$inc`, etc.) in update operations
- Be careful with the syntax when using `ObjectId`
- `updateOne()` updates the first matching document
- `updateMany()` updates all matching documents
- The update operation returns a result object with counts of matched and modified documents

---

# Field Renaming and Document Deletion

This section demonstrates how to rename fields and delete documents or specific fields in MongoDB.

## Renaming Fields

### 1. Rename a Single Field

Using the `$rename` operator to change a field name:

```mongodb
db.products.updateMany(
  {},
  { $rename: { "price": "productPrice" }}
)
```

**Result:** Renames "price" to "productPrice" in all documents

### 2. Rename Fields in Embedded Documents

To rename fields within embedded documents:

```mongodb
db.comments.updateMany(
  {},
  { $rename: { "metadata.views": "metadata.viewCount" }}
)
```

### 3. Rename Array Fields

Renaming fields inside array elements (not recommended, use with caution):

```mongodb
db.comments.updateMany(
  {},
  { $rename: { "comments.$[].text": "comments.$[].content" }}
)
```

## Deleting Documents and Fields

### 1. Delete a Single Document

```mongodb
db.products.deleteOne({ name: "Kitchen Blender" })
```

**Result:**

```json
{
  "acknowledged": true,
  "deletedCount": 1
}
```

### 2. Delete Multiple Documents

```mongodb
db.products.deleteMany({ isFeatured: false })
```

### 3. Remove a Field

Using `$unset` to remove a field from documents:

```mongodb
db.products.updateMany(
  {},
  { $unset: { "oldField": "" }}
)
```

### 4. Remove a Field from Embedded Documents

```mongodb
db.products.updateMany(
  {},
  { $unset: { "metadata.oldProperty": "" }}
)
```

### 5. Remove an Element from Array

Using `$pull` to remove array elements:

```mongodb
db.products.updateOne(
  { name: "Kitchen Blender" },
  { $pull: { colors: "#ffffff" }}
)
```

## Notes

- The `$rename` operator can't move fields between arrays
- Use `$unset` to remove fields completely
- `deleteMany()` without a filter will delete ALL documents
- `$pull` is used for removing elements from arrays
- Always backup data before performing bulk delete operations
- Field renames in embedded documents require the full path
