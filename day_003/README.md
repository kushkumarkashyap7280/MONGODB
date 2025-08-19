# Day 003 — MongoDB quick guide

Keep it short and practical.

## What you did
- Imported data into `shop` DB: `products`, `sales`, `categories`.
- Exported `categories` to JSON.
- Ran basic `price` queries in `mongosh`.

## Commands
```powershell
# Import
mongoimport --db shop --collection products --file "C:\Users\kushk\OneDrive\Desktop\MONGODB\day_003\MongoDB Import Files\products.json"
mongoimport --db shop --collection sales --file "C:\Users\kushk\OneDrive\Desktop\MONGODB\day_003\MongoDB Import Files\sales.json" --jsonArray
mongoimport --db shop --collection categories --file "C:\Users\kushk\OneDrive\Desktop\MONGODB\day_003\MongoDB Import Files\categories.json"

# Export
mongoexport --db shop --collection categories --out "C:\Users\kushk\OneDrive\Desktop\MONGODB\day_003\MONGODB\testexport.json" --jsonArray
```

## Queries
```javascript
use shop
db.products.find({ price: { $eq: 129 } }).count()
db.products.find({ price: { $ne: 129 } }).count()
db.products.find({ price: { $gt: 129 } }).count()
db.products.find({ price: { $lte: 129 } }).count()
db.products.find({ price: { $in: [1,2,3,4,5] } }).count()
```

## Cursor methods
- `limit(n)`: cap number of docs returned.
- `skip(n)`: skip n docs (useful for pagination).
- `sort({ field: 1|-1 })`: 1 ascending, -1 descending.

```javascript
// Limit
db.products.find({ price: { $nin: [1] } }).limit(1)

// Limit multiple
db.products.find({ price: 299 }).limit(3)

// Limit + skip (page 2 with page size 3)
db.products.find({ price: 299 }).limit(3).skip(1)

// Sort ascending by price (correct)
db.products.find({ price: { $ne: 299 } }).limit(3).sort({ price: 1 })

// Sort only (ascending by price)
db.products.find({ price: { $ne: 299 } }).sort({ price: 1 })
```

## Logical operators
- `$and`: all conditions must match. (Implicit AND by default.)
- `$or`: any condition matches.
- `$not`: negate a field condition (wraps another operator like `$gte`).
- `$nor`: none of the conditions match.

```javascript
// AND (explicit)
db.products.find({
  $and: [ { price: { $gte: 100 } }, { isFeatured: true } ]
}).limit(3)

// AND (implicit)
db.products.find({ price: { $gte: 100 }, isFeatured: true }).limit(3)

// OR
db.products.find({ $or: [ { price: 299 }, { isFeatured: true } ] }).limit(3)

// NOT (negate operator on a field)
db.products.find({ price: { $not: { $gte: 300 } } }).limit(3)

// NOR (neither featured nor cheap)
db.products.find({ $nor: [ { isFeatured: true }, { price: { $lt: 50 } } ] }).limit(3)
```

## $expr examples
- Use aggregation expressions inside `find()` filters.
- Operators like `$gt`, `$lt`, `$add`, `$multiply` take arrays of arguments.

```javascript
// Field vs constant: price > 1340
db.products.find({ $expr: { $gt: ["$price", 1340] } })

// Computed vs field: (quantity * price) > targetPrice
db.sales.find({
  $expr: {
    $gt: [ { $multiply: ["$quantity", "$price"] }, "$targetPrice" ]
  }
})

// Another variant: quantity + price < targetPrice
db.sales.find({ $expr: { $lt: [ { $add: ["$quantity", "$price"] }, "$targetPrice" ] } })
```

Tip (PowerShell -eval): wrap the whole expression in single quotes so `$price` isn't expanded.

## Element & array operators
- `$exists`: field present or not present.
- `$type`: field BSON type matches.
- `$size`: exact array length. For comparisons, combine with `$expr` + `$size`.
- `$elemMatch`: at least one array element matches all conditions.
- `$all`: array contains all listed values.

```javascript
// Field existence
db.products.find({ price: { $exists: true } })
db.products.find({ price: { $exists: false } })

// Type checks (use string aliases)
db.products.find({ price: { $type: 'number' } })
db.products.find({ price: { $type: ['number', 'string'] } })

// Array length exact
db.comments.find({ comments: { $size: 4 } })

// Array length comparisons via $expr
db.comments.find({ $expr: { $gt: [ { $size: '$comments' }, 3 ] } })
db.comments.find({ $expr: { $lte: [ { $size: '$comments' }, 2 ] } })

// Array of subdocuments: match one element with both conditions
db.comments.find({
  comments: { $elemMatch: { user: 'Eva', text: { $exists: true } } }
})

// Array contains both values
db.products.find({ colors: { $all: ['#000000', '#ff9900'] } })
```

## Notes
- Use `$ne` (not `$neq`).
- If path errors occur, verify the exact folder name `MONGODB` and full path.
- Full terminal log removed for brevity.
- `sort()` must take an object. Calling `sort(1)` is invalid: "Invalid sort format: 1".
- Chain order doesn't matter for `limit`, `skip`, `sort` in mongosh; the server will optimize.
