# Day 5: MongoDB Indexing and Query Performance Analysis

This session explores MongoDB indexing and how to analyze query performance using the `explain()` method.

## Query Performance Analysis

### 1. Checking Document Count with Price Filter

```mongodb
db.products.find({price : {$gt : 100}}).count()
```

**Result:** 9,216 documents found

### 2. Basic Query Explanation

```mongodb
db.products.find({price : {$gt : 100}}).explain()
```

**Key Findings:**

- Stage: `COLLSCAN` (Collection Scan)
- No indexes being used
- Full collection scan required
- Query uses a forward direction scan

### 3. Detailed Query Performance Analysis

```mongodb
db.products.find({price : {$gt : 100}}).explain('executionStats')
```

**Performance Statistics:**

- Documents Returned: 9,216
- Execution Time: 4 milliseconds
- Documents Examined: 10,355 (entire collection)
- Keys Examined: 0 (no index used)

### 4. Name Query Analysis

```mongodb
db.products.find({name : 'Air Fryer'}).explain('executionStats')
```

**Performance Statistics:**

- Documents Returned: 3
- Execution Time: 6 milliseconds
- Documents Examined: 10,355 (entire collection)
- Stage: `COLLSCAN` (Collection Scan)

## Understanding the Output

### Collection Scan (COLLSCAN)

Both queries are using collection scans, which means:

- MongoDB has to check every document in the collection
- No indexes are being utilized
- Performance will degrade as the collection grows

### Key Performance Indicators

1. **Documents Examined vs Returned:**

   - Price Query: Examined 10,355 to return 9,216 (89% relevant)
   - Name Query: Examined 10,355 to return 3 (0.029% relevant)

2. **Execution Time:**
   - Price Query: 4ms
   - Name Query: 6ms (slower despite returning fewer documents)

## Recommendations

1. **Create Indexes for Frequent Queries:**

   ```mongodb
   db.products.createIndex({ price: 1 })
   db.products.createIndex({ name: 1 })
   ```

2. **Consider Compound Indexes** for queries that frequently filter on multiple fields:

   ```mongodb
   db.products.createIndex({ name: 1, price: 1 })
   ```

3. **Monitor Index Usage:**
   - Use `explain()` regularly to verify index usage
   - Remove unused indexes to reduce overhead

## Best Practices

1. Create indexes to support common queries
2. Avoid scanning entire collections for selective queries
3. Use `explain()` with 'executionStats' to analyze query performance
4. Balance between query performance and write performance
5. Consider the impact of indexes on write operations

## Notes

- Collection scans (COLLSCAN) indicate no index usage
- High ratio of examined/returned documents suggests need for indexing
- Index creation can impact write performance
- Regular monitoring of query performance is essential

---

# Creating and Managing Indexes

## Viewing and Creating Indexes

### 1. View Existing Indexes

```mongodb
db.products.getIndexes()
```

**Initial Result:**

```json
[{ "v": 2, "key": { "_id": 1 }, "name": "_id_" }]
```

Note: By default, MongoDB creates an index on the `_id` field.

### 2. Create New Index

```mongodb
db.products.createIndex({name : 1})
```

**Result:** `name_1`

### 3. Verify Index Creation

```mongodb
db.products.getIndexes()
```

**Result:**

```json
[
  { "v": 2, "key": { "_id": 1 }, "name": "_id_" },
  { "v": 2, "key": { "name": 1 }, "name": "name_1" }
]
```

## Performance Impact Analysis

### Query Performance After Index Creation

```mongodb
db.products.find({name : 'Air Fryer'}).explain('executionStats')
```

**Before Index:**

- Stage: COLLSCAN (Collection Scan)
- Documents Examined: 10,355
- Documents Returned: 3
- Execution Time: 6ms

**After Index:**

- Stage: IXSCAN (Index Scan)
- Documents Examined: 3
- Documents Returned: 3
- Execution Time: ~0ms
- Keys Examined: 3

## When to Use Indexes

1. **High Read-to-Write Ratio:**

   - Frequently queried fields
   - Fields used in sorting operations
   - Fields used in aggregation operations

2. **Selective Queries:**

   - Queries that return < 30% of documents
   - Unique or near-unique values
   - Common filtering fields

3. **Large Collections:**

   - Collections with many documents
   - When full collection scans are expensive

4. **Relationship Fields:**
   - Foreign key fields
   - Fields used in $lookup operations

## When to Avoid Indexes

1. **High Write Load:**

   - Collections with frequent inserts/updates
   - Time-series data with constant writes

2. **Low Selectivity:**

   - Fields with few unique values
   - Queries returning > 30% of documents
   - Boolean fields (true/false)

3. **Small Collections:**

   - Collections with few documents
   - When collection scan is faster

4. **Resource Constraints:**

   - Limited RAM
   - Already many indexes exist

5. **Unused Indexes:**
   - Indexes not used by queries
   - Redundant indexes

## Index Types and Use Cases

1. **Single Field Index:**

   ```mongodb
   db.products.createIndex({ price: 1 })
   ```

   - Simple queries on one field
   - Basic sorting operations

2. **Compound Index:**

   ```mongodb
   db.products.createIndex({ category: 1, price: -1 })
   ```

   - Multi-field queries
   - Complex sorting requirements

3. **Text Index:**

   ```mongodb
   db.products.createIndex({ description: "text" })
   ```

   - Full-text search
   - Language-specific searches

4. **Partial Index:**
   ```mongodb
   db.products.createIndex(
     { price: 1 },
     { partialFilterExpression: { inStock: true }}
   )
   ```
   - Subset of documents
   - Reduced index size
