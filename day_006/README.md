# Day 006 - MongoDB Aggregation Framework

## Overview

This session covers MongoDB Aggregation operations using the `shop` database. The aggregation framework provides powerful data processing and transformation capabilities for MongoDB collections.

## Environment Setup

- **MongoDB Version**: 8.0.12
- **Mongosh Version**: 2.5.6
- **Database**: `shop`
- **Collections**: `categories`, `comments`, `products`, `sales`

## Database Collections

```javascript
show collections
// Output:
// categories
// comments
// products
// sales
```

## Aggregation Pipeline Examples

### 1. $match Operator - Filtering Documents

#### Example 1: Find products by exact name match

```javascript
db.products.aggregate([{ $match: { name: "Sleek Wooden Tuna" } }]);
```

**Result**: Returns 2 products with the name "Sleek Wooden Tuna" with different prices (101 and 363).

#### Example 2: Find products with price greater than 50

```javascript
db.products.aggregate([{ $match: { price: { $gt: 50 } } }]);
```

**Result**: Returns all products priced above $50, including:

- Smartphone Model X ($699)
- Laptop Pro ($1299)
- Smart Watch ($249)
- Various other products ranging from $59 to $899

### 2. $group Operator - Grouping and Aggregating Data

#### Example 1: Count total products by company

```javascript
db.products.aggregate([
  {
    $group: {
      _id: "$company",
      totalProducts: { $sum: 1 },
    },
  },
]);
```

**Result**: Groups products by company ID and counts total products per company.

#### Example 2: Combined $match and $group - High-value products by company

```javascript
db.products.aggregate([
  { $match: { price: { $gt: 900 } } },
  {
    $group: {
      _id: "$company",
      totalProducts: { $sum: 1 },
    },
  },
]);
```

**Result**: First filters products over $900, then groups by company showing count of premium products per company.

### 3. Working with Sales Data

#### Sales Collection Structure

```javascript
db.sales.find();
```

Sample data:

```json
[
  { "_id": 4, "quantity": 5, "price": 55, "targetPrice": 150 },
  { "_id": 3, "quantity": 6, "price": 35, "targetPrice": 100 },
  { "_id": 2, "quantity": 5, "price": 25, "targetPrice": 100 },
  { "_id": 1, "quantity": 10, "price": 15, "targetPrice": 120 },
  { "_id": 5, "quantity": 5, "price": 55, "targetPrice": 150 }
]
```

#### Example: Calculate statistics for sales with quantity = 5

```javascript
db.sales.aggregate([
  { $match: { quantity: 5 } },
  {
    $group: {
      _id: "$quantity",
      totalPrice: { $sum: "$price" },
      avgPrice: { $avg: "$price" },
    },
  },
]);
```

**Result**:

```json
[
  {
    "_id": 5,
    "totalPrice": 135,
    "avgPrice": 45
  }
]
```

## Common Mistakes and Solutions

### ❌ Incorrect Field Reference

```javascript
// Wrong - using $ inside quotes for field names
{ $match: { "$quantity": 5 } }
{ $match: { '$quantity': 5 } }
```

### ✅ Correct Field Reference

```javascript
// Correct - field name without $ in match conditions
{
  $match: {
    quantity: 5;
  }
}
```

## Key Learning Points

1. **$match**: Filters documents based on specified conditions (similar to WHERE in SQL)
2. **$group**: Groups documents by specified fields and performs aggregations
3. **Pipeline Order**: The order of stages in aggregation pipeline matters
4. **Field References**: Use `"$fieldName"` for referencing fields in aggregation operators
5. **Aggregation Operators**:
   - `$sum`: Calculates sum of values
   - `$avg`: Calculates average of values
   - `$gt`: Greater than comparison

## Product Data Insights

From the examples, we can see the `products` collection contains:

- Product names, companies, prices, colors
- Categories and images
- Boolean `isFeatured` flag
- Price ranges from under $100 to over $1000
- Various product categories (electronics, furniture, accessories, etc.)

## Complete Aggregation Pipeline Operators Reference

### Stage Operators (Pipeline Stages)

#### 1. **$match** - Filtering Documents

Filters documents to pass only those that match the specified condition(s).

```javascript
// Filter by single condition
db.products.aggregate([{ $match: { price: { $gte: 100 } } }]);

// Filter by multiple conditions
db.products.aggregate([
  {
    $match: {
      price: { $gte: 100 },
      isFeatured: true,
    },
  },
]);
```

#### 2. **$project** - Field Selection and Transformation

Reshapes documents by including, excluding, or adding new fields.

```javascript
// Include specific fields
db.products.aggregate([{ $project: { name: 1, price: 1, _id: 0 } }]);

// Create computed fields
db.products.aggregate([
  {
    $project: {
      name: 1,
      price: 1,
      discountedPrice: { $multiply: ["$price", 0.9] },
      category: 1,
    },
  },
]);
```

#### 3. **$group** - Grouping and Aggregation

Groups documents by specified fields and performs aggregations.

```javascript
// Multiple aggregation operations
db.sales.aggregate([
  {
    $group: {
      _id: "$category",
      totalSales: { $sum: "$price" },
      averagePrice: { $avg: "$price" },
      minPrice: { $min: "$price" },
      maxPrice: { $max: "$price" },
      count: { $sum: 1 },
    },
  },
]);
```

#### 4. **$sort** - Sorting Documents

Sorts documents by specified fields.

```javascript
// Sort by single field
db.products.aggregate([
  { $sort: { price: -1 } }, // -1 for descending, 1 for ascending
]);

// Sort by multiple fields
db.products.aggregate([{ $sort: { category: 1, price: -1 } }]);
```

#### 5. **$limit** - Limiting Results

Limits the number of documents passed to the next stage.

```javascript
// Get top 5 most expensive products
db.products.aggregate([{ $sort: { price: -1 } }, { $limit: 5 }]);
```

#### 6. **$skip** - Skipping Documents

Skips a specified number of documents.

```javascript
// Skip first 10 documents (pagination)
db.products.aggregate([{ $skip: 10 }, { $limit: 5 }]);
```

#### 7. **$unwind** - Array Deconstruction

Deconstructs an array field to output a document for each element.

```javascript
// Unwind colors array
db.products.aggregate([
  { $unwind: "$colors" },
  { $group: { _id: "$colors", count: { $sum: 1 } } },
]);

// Unwind with preserveNullAndEmptyArrays
db.products.aggregate([
  {
    $unwind: {
      path: "$colors",
      preserveNullAndEmptyArrays: true,
    },
  },
]);
```

#### 8. **$lookup** - Left Outer Join

Performs a left outer join with another collection.

```javascript
// Join products with categories
db.products.aggregate([
  {
    $lookup: {
      from: "categories",
      localField: "category",
      foreignField: "_id",
      as: "categoryInfo",
    },
  },
]);

// Lookup with pipeline (advanced)
db.products.aggregate([
  {
    $lookup: {
      from: "comments",
      let: { productId: "$_id" },
      pipeline: [
        {
          $match: {
            $expr: { $eq: ["$productId", "$$productId"] },
          },
        },
        { $sort: { date: -1 } },
        { $limit: 3 },
      ],
      as: "recentComments",
    },
  },
]);
```

#### 9. **$addFields** - Adding New Fields

Adds new fields to documents.

```javascript
db.products.aggregate([
  {
    $addFields: {
      discountedPrice: { $multiply: ["$price", 0.9] },
      isExpensive: { $gte: ["$price", 500] },
    },
  },
]);
```

#### 10. **$count** - Count Documents

Returns the count of documents.

```javascript
db.products.aggregate([
  { $match: { price: { $gte: 100 } } },
  { $count: "expensiveProducts" },
]);
```

#### 11. **$facet** - Multi-faceted Aggregation

Processes multiple aggregation pipelines within a single stage.

```javascript
db.products.aggregate([
  {
    $facet: {
      priceRanges: [
        {
          $group: {
            _id: {
              $switch: {
                branches: [
                  { case: { $lt: ["$price", 100] }, then: "Budget" },
                  { case: { $lt: ["$price", 500] }, then: "Mid-range" },
                ],
                default: "Premium",
              },
            },
            count: { $sum: 1 },
          },
        },
      ],
      topCategories: [
        { $group: { _id: "$category", count: { $sum: 1 } } },
        { $sort: { count: -1 } },
        { $limit: 5 },
      ],
    },
  },
]);
```

#### 12. **$bucket** - Bucketing Documents

Categorizes documents into groups (buckets) based on specified boundaries.

```javascript
db.products.aggregate([
  {
    $bucket: {
      groupBy: "$price",
      boundaries: [0, 100, 500, 1000, Infinity],
      default: "Other",
      output: {
        count: { $sum: 1 },
        averagePrice: { $avg: "$price" },
      },
    },
  },
]);
```

### Expression Operators

#### Arithmetic Operators

```javascript
// Mathematical operations
{
  $project: {
    name: 1,
    price: 1,
    tax: { $multiply: ["$price", 0.1] },
    total: { $add: ["$price", { $multiply: ["$price", 0.1] }] },
    discount: { $subtract: ["$price", { $multiply: ["$price", 0.1] }] },
    half: { $divide: ["$price", 2] }
  }
}
```

#### Comparison Operators

```javascript
{
  $project: {
    name: 1,
    price: 1,
    isExpensive: { $gte: ["$price", 500] },
    isBudget: { $lt: ["$price", 100] },
    isEqual: { $eq: ["$category", "electronics"] }
  }
}
```

#### Logical Operators

```javascript
{
  $project: {
    name: 1,
    price: 1,
    category: 1,
    isGoodDeal: {
      $and: [
        { $gte: ["$price", 100] },
        { $lte: ["$price", 300] },
        { $eq: ["$isFeatured", true] }
      ]
    }
  }
}
```

#### String Operators

```javascript
{
  $project: {
    name: 1,
    upperName: { $toUpper: "$name" },
    lowerName: { $toLower: "$name" },
    nameLength: { $strLenCP: "$name" },
    nameSubstring: { $substr: ["$name", 0, 10] },
    nameConcat: { $concat: ["Product: ", "$name"] }
  }
}
```

#### Date Operators

```javascript
{
  $project: {
    name: 1,
    createdAt: 1,
    year: { $year: "$createdAt" },
    month: { $month: "$createdAt" },
    dayOfWeek: { $dayOfWeek: "$createdAt" },
    age: {
      $divide: [
        { $subtract: [new Date(), "$createdAt"] },
        1000 * 60 * 60 * 24  // Convert to days
      ]
    }
  }
}
```

#### Array Operators

```javascript
{
  $project: {
    name: 1,
    colors: 1,
    colorCount: { $size: "$colors" },
    firstColor: { $arrayElemAt: ["$colors", 0] },
    hasRedColor: { $in: ["red", "$colors"] },
    colorSlice: { $slice: ["$colors", 2] }
  }
}
```

## Advanced Aggregation Patterns

### 1. **Pipeline Optimization Pattern**

```javascript
// ✅ Optimized - filter early, then process
db.products.aggregate([
  { $match: { price: { $gte: 100 } } }, // Filter first
  {
    $lookup: {
      /* join operation */
    },
  },
  { $sort: { price: -1 } },
  { $limit: 10 },
]);

// ❌ Not optimized - expensive operations before filtering
db.products.aggregate([
  {
    $lookup: {
      /* join operation */
    },
  },
  { $match: { price: { $gte: 100 } } }, // Filter last
  { $sort: { price: -1 } },
]);
```

### 2. **Conditional Aggregation Pattern**

```javascript
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      totalProducts: { $sum: 1 },
      expensiveCount: {
        $sum: { $cond: [{ $gte: ["$price", 500] }, 1, 0] },
      },
      averageExpensivePrice: {
        $avg: { $cond: [{ $gte: ["$price", 500] }, "$price", null] },
      },
    },
  },
]);
```

### 3. **Text Search Integration**

```javascript
db.products.aggregate([
  { $match: { $text: { $search: "smartphone wireless" } } },
  { $addFields: { score: { $meta: "textScore" } } },
  { $sort: { score: { $meta: "textScore" } } },
]);
```

## Best Practices

### 1. **Performance Optimization**

- **Filter Early**: Use `$match` as early as possible in the pipeline
- **Limit Data**: Use `$limit` and `$skip` for pagination
- **Project Only Needed Fields**: Use `$project` to reduce document size
- **Index Usage**: Ensure `$match` and `$sort` operations can use indexes

```javascript
// ✅ Good practice
db.products.aggregate([
  { $match: { category: "electronics" } }, // Uses index
  { $project: { name: 1, price: 1 } }, // Reduce document size
  { $sort: { price: -1 } }, // Can use index on price
  { $limit: 20 }, // Limit results
]);
```

### 2. **Memory Management**

- **allowDiskUse**: For large datasets, allow disk usage

```javascript
db.products.aggregate(pipeline, { allowDiskUse: true });
```

- **Batch Size**: Control batch size for large result sets

```javascript
db.products.aggregate(pipeline, { cursor: { batchSize: 100 } });
```

### 3. **Error Handling and Validation**

```javascript
// Use $ifNull to handle missing fields
{
  $project: {
    name: 1,
    price: { $ifNull: ["$price", 0] },
    category: { $ifNull: ["$category", "uncategorized"] }
  }
}

// Use $type to validate field types
{
  $match: {
    price: { $type: "number" }
  }
}
```

### 4. **Readability and Maintainability**

```javascript
// ✅ Break complex pipelines into readable stages
const matchStage = { $match: { category: "electronics" } };
const projectStage = {
  $project: {
    name: 1,
    price: 1,
    discountedPrice: { $multiply: ["$price", 0.9] },
  },
};
const sortStage = { $sort: { price: -1 } };

db.products.aggregate([matchStage, projectStage, sortStage]);
```

## Important Considerations

### 1. **Memory Limits**

- Pipeline stages have a 100MB memory limit by default
- Use `allowDiskUse: true` for operations exceeding this limit
- Consider breaking large operations into smaller chunks

### 2. **Index Utilization**

- `$match` and `$sort` can use indexes if they're the first stages
- Compound indexes can benefit multiple pipeline stages
- Use `explain()` to verify index usage

### 3. **Pipeline Stage Order Matters**

```javascript
// ✅ Efficient order
[
  { $match: {} }, // Filter first
  { $lookup: {} }, // Join data
  { $unwind: {} }, // Unwind arrays
  { $group: {} }, // Group/aggregate
  { $sort: {} }, // Sort results
  { $limit: {} }, // Limit output
];
```

### 4. **Data Types and Precision**

- Be aware of floating-point precision in calculations
- Use `$toLong`, `$toInt`, `$toDouble` for type conversions
- Handle null and undefined values explicitly

### 5. **Security Considerations**

- Validate input parameters before using in aggregation
- Use parameterized queries to prevent injection attacks
- Limit pipeline complexity to prevent DoS attacks

## Common Use Cases

### 1. **Analytics and Reporting**

```javascript
// Sales report by month
db.sales.aggregate([
  {
    $group: {
      _id: {
        year: { $year: "$date" },
        month: { $month: "$date" },
      },
      totalSales: { $sum: "$amount" },
      orderCount: { $sum: 1 },
      averageOrderValue: { $avg: "$amount" },
    },
  },
  { $sort: { "_id.year": 1, "_id.month": 1 } },
]);
```

### 2. **Data Transformation**

```javascript
// Normalize product data
db.products.aggregate([
  {
    $addFields: {
      normalizedName: { $toLower: { $trim: { input: "$name" } } },
      priceCategory: {
        $switch: {
          branches: [
            { case: { $lt: ["$price", 100] }, then: "Budget" },
            { case: { $lt: ["$price", 500] }, then: "Mid-range" },
          ],
          default: "Premium",
        },
      },
    },
  },
]);
```

### 3. **Data Validation**

```javascript
// Find data quality issues
db.products.aggregate([
  {
    $project: {
      name: 1,
      issues: {
        $concatArrays: [
          { $cond: [{ $eq: ["$name", ""] }, ["Empty name"], []] },
          { $cond: [{ $lte: ["$price", 0] }, ["Invalid price"], []] },
          { $cond: [{ $eq: [{ $size: "$colors" }, 0] }, ["No colors"], []] },
        ],
      },
    },
  },
  { $match: { issues: { $ne: [] } } },
]);
```

## Next Steps

- Explore more aggregation operators like `$replaceRoot`, `$redact`, `$geoNear`
- Learn about `$merge` and `$out` for outputting results to collections
- Practice with `$graphLookup` for recursive/hierarchical data
- Study performance optimization techniques and index strategies
- Experiment with aggregation framework in different use cases (analytics, ETL, reporting)
