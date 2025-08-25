<div align="center">
  <h1>📊 MongoDB Learning Journey</h1>
  
  <div style="display: flex; justify-content: center; align-items: center; gap: 20px; margin: 20px 0;">
    <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/mongodb/mongodb-original-wordmark.svg" alt="MongoDB" width="200"/>
  </div>
  
  <p>A structured guide to mastering MongoDB, from fundamentals to advanced concepts</p>
  
  <div>
    <img src="https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white" alt="MongoDB">
    <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" alt="Node.js">
    <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript">
  </div>
</div>

## 🚀 Quick Start

### Prerequisites

- [MongoDB Community Server](https://www.mongodb.com/try/download/community)
- [MongoDB Shell (mongosh)](https://www.mongodb.com/try/download/shell)
- [Node.js](https://nodejs.org/) (v16+ recommended)

### Installation

1. **Windows**:

   - Download and run the MSI installer from [MongoDB Download Center](https://www.mongodb.com/try/download/community)
   - Follow the installation wizard

2. **macOS**:

   ```bash
   brew tap mongodb/brew
   brew install mongodb-community
   ```

3. **Ubuntu/Debian**:
   ```bash
   sudo apt-get install -y mongodb
   ```

### Verify Installation

```bash
mongod --version
mongosh --version
```

## 📅 Learning Path

### Day 1: Getting Started

- [Day 1: Setup & Basics](./day_001/README.md)
  - MongoDB installation
  - Basic commands
  - Database and collection operations

### Day 2: CRUD Operations

- [Day 2: CRUD Operations](./day_002/README.md)
  - Insert one/many, find/findOne with projection
  - Update one/many with $set
  - Delete one/many, bulk operations, common mistakes

### Day 3: Query Methods & Operators

- [Day 3: Query Methods & Operators](./day_003/README.md)
  - Cursor methods: limit, skip, sort
  - Logical operators: $and, $or, $not, $nor
  - $expr usage (computed comparisons)
  - Element & array operators: $exists, $type, $size, $elemMatch, $all

### Day 4: Advanced Querying & Projections

- [Day 4: Advanced Querying & Projections](./day_004/README.md)
  - Projection techniques and field selection
  - Embedded document queries with dot notation
  - Array querying with $size, $all, $elemMatch
  - Update operations: updateOne, updateMany
  - Field renaming with $rename and deletion with $unset

### Day 5: Indexing & Performance

- [Day 5: Indexing & Performance](./day_005/README.md)
  - Query performance analysis with explain()
  - Understanding COLLSCAN vs IXSCAN
  - Creating and managing indexes
  - Index types: single field, compound, text, partial
  - Best practices for when to use/avoid indexes

### Day 6: Aggregation Framework

- [Day 6: Aggregation Framework](./day_006/README.md)
  - Complete aggregation pipeline operators reference
  - Stage operators: $match, $project, $group, $sort, $limit, $lookup
  - Expression operators: arithmetic, comparison, logical, string, date, array
  - Advanced patterns: optimization, conditional aggregation, faceted search
  - Best practices and performance considerations

## 🎯 **What You'll Learn:**

By completing this 6-day MongoDB journey, you'll master:

- **Database Fundamentals**: Setup, CRUD operations, and basic querying
- **Advanced Querying**: Complex filters, projections, and embedded document queries
- **Performance Optimization**: Indexing strategies and query performance analysis
- **Data Aggregation**: Complete aggregation framework with real-world patterns
- **Production Best Practices**: Error handling, security, and optimization techniques

## 🏆 **Course Completion:**

This series covers all essential MongoDB concepts needed for production applications. After completing all 6 days, you'll be equipped to build robust, scalable applications with MongoDB.

## 📚 Resources

### Documentation

- [MongoDB Manual](https://docs.mongodb.com/manual/)
- [MongoDB University](https://university.mongodb.com/)
- [MongoDB Blog](https://www.mongodb.com/blog)

### Tools

- [MongoDB Compass](https://www.mongodb.com/products/compass) - GUI for MongoDB
- [MongoDB Charts](https://www.mongodb.com/products/charts) - Data visualization
- [MongoDB Database Tools](https://www.mongodb.com/try/download/database-tools) - Command-line tools

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

<div align="center">
  Made with ❤️ by Kush Kumar
</div>
