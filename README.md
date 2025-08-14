<div align="center">
  <h1>📊 MongoDB Learning Journey</h1>
  
  <div style="display: flex; justify-content: center; align-items: center; gap: 20px; margin: 20px 0;">
    <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/mongodb/mongodb-original-wordmark.svg" alt="MongoDB" width="200"/>
  </div>
  
  <p>A comprehensive guide to mastering MongoDB, from fundamentals to advanced concepts</p>
  
  <div>
    <img src="https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white" alt="MongoDB">
    <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" alt="Node.js">
    <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript">
  </div>
</div>

## 📋 Table of Contents

- [🚀 Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Configuration](#configuration)
- [📚 Core Concepts](#-core-concepts)
  - [Documents & Collections](#documents--collections)
  - [CRUD Operations](#crud-operations)
  - [Indexes](#indexes)
  - [Aggregation Framework](#aggregation-framework)
  - [Data Modeling](#data-modeling)
- [🛠️ Advanced Topics](#%EF%B8%8F-advanced-topics)
  - [Transactions](#transactions)
  - [Replication](#replication)
  - [Sharding](#sharding)
  - [Performance Optimization](#performance-optimization)
- [🔒 Security](#-security)
  - [Authentication](#authentication)
  - [Authorization](#authorization)
  - [Encryption](#encryption)
- [🧪 Testing](#-testing)
- [🚀 Deployment](#-deployment)
- [📚 Resources](#-resources)
- [📝 License](#-license)

## 🚀 Getting Started

### Prerequisites

- [MongoDB Server](https://www.mongodb.com/try/download/community) (v6.0+ recommended)
- [MongoDB Shell (mongosh)](https://www.mongodb.com/try/download/shell)
- [Node.js](https://nodejs.org/) (v16+ recommended)
- [MongoDB Compass](https://www.mongodb.com/try/download/compass) (GUI Tool, optional but recommended)

### Installation

1. **Install MongoDB Community Edition**
   ```bash
   # For macOS (using Homebrew)
   brew tap mongodb/brew
   brew install mongodb-community
   
   # For Ubuntu/Debian
   sudo apt-get install -y mongodb
   
   # For Windows
   # Download and run the installer from MongoDB website
   ```

2. **Start MongoDB Service**
   ```bash
   # macOS (using Homebrew)
   brew services start mongodb-community
   
   # Ubuntu/Debian
   sudo systemctl start mongod
   
   # Verify the service is running
   mongosh --eval 'db.runCommand({ connectionStatus: 1 })'
   ```

### Configuration

1. **Environment Variables**
   Create a `.env` file in your project root:
   ```env
   MONGODB_URI=mongodb://localhost:27017/your_database_name
   MONGODB_DB_NAME=your_database_name
   MONGODB_USERNAME=your_username
   MONGODB_PASSWORD=your_secure_password
   ```

2. **Node.js Project Setup**
   ```bash
   # Initialize a new Node.js project
   npm init -y
   
   # Install MongoDB Node.js driver
   npm install mongodb
   
   # Install development dependencies
   npm install --save-dev dotenv nodemon
   ```

## 📚 Core Concepts

### Documents & Collections

MongoDB stores data in flexible, JSON-like documents, meaning fields can vary from document to document and data structure can be changed over time.

```javascript
// Example document
{
  _id: ObjectId("60d5ec9e8b1c9f4a2c8b4567"),
  username: "johndoe",
  email: "john@example.com",
  age: 30,
  address: {
    street: "123 Main St",
    city: "New York",
    country: "USA"
  },
  tags: ["developer", "mongodb", "nodejs"],
  createdAt: ISODate("2023-01-15T10:30:00Z")
}
```

### CRUD Operations

#### Create
```javascript
// Insert a single document
db.collection('users').insertOne({
  name: "John Doe",
  email: "john@example.com",
  role: "admin"
});

// Insert multiple documents
db.collection('users').insertMany([
  { name: "Alice", role: "user" },
  { name: "Bob", role: "editor" }
]);
```

#### Read
```javascript
// Find all documents
db.collection('users').find({});

// Find with query
db.collection('users').find({ role: "admin" });

// Find one document
db.collection('users').findOne({ email: "john@example.com" });
```

#### Update
```javascript
// Update one document
db.collection('users').updateOne(
  { email: "john@example.com" },
  { $set: { role: "superadmin" } }
);

// Update multiple documents
db.collection('users').updateMany(
  { role: "user" },
  { $set: { status: "active" } }
);
```

#### Delete
```javascript
// Delete one document
db.collection('users').deleteOne({ email: "john@example.com" });

// Delete multiple documents
db.collection('users').deleteMany({ status: "inactive" });
```

### Indexes

Indexes support the efficient execution of queries in MongoDB. Without indexes, MongoDB must perform a collection scan, i.e., scan every document in a collection.

```javascript
// Create a single field index
db.collection('users').createIndex({ email: 1 });

// Create a compound index
db.collection('users').createIndex({ lastName: 1, firstName: 1 });

// Create a text index for text search
db.collection('articles').createIndex({ content: "text" });

// List all indexes
db.collection('users').getIndexes();
```

### Aggregation Framework

The aggregation pipeline provides efficient data processing and transformation.

```javascript
db.orders.aggregate([
  // Stage 1: Filter documents
  {
    $match: {
      status: "completed",
      orderDate: { $gte: new Date("2023-01-01") }
    }
  },
  
  // Stage 2: Group by product and calculate total sales
  {
    $group: {
      _id: "$product",
      totalSales: { $sum: "$amount" },
      averageQuantity: { $avg: "$quantity" },
      count: { $sum: 1 }
    }
  },
  
  // Stage 3: Sort by total sales (descending)
  {
    $sort: { totalSales: -1 }
  },
  
  // Stage 4: Limit to top 10 results
  {
    $limit: 10
  }
]);
```

### Data Modeling

#### Embedded Documents
```javascript
// One-to-Few: Embed directly in the parent document
{
  _id: 1,
  name: "John Doe",
  addresses: [
    { type: "home", street: "123 Main St", city: "New York" },
    { type: "work", street: "456 Work Ave", city: "Boston" }
  ]
}
```

#### Document References
```javascript
// One-to-Many: Use references
// In the posts collection
{
  _id: 1,
  title: "MongoDB Best Practices",
  content: "...",
  author: ObjectId("60d5ec9e8b1c9f4a2c8b4567"),
  tags: ["mongodb", "database", "nosql"]
}

// In the users collection
{
  _id: ObjectId("60d5ec9e8b1c9f4a2c8b4567"),
  name: "John Doe",
  email: "john@example.com"
}
```

## 🛠️ Advanced Topics

### Transactions

MongoDB supports multi-document transactions on replica sets and sharded clusters.

```javascript
const session = client.startSession();

try {
  session.startTransaction();
  
  // Operations in the transaction
  await db.collection('accounts').updateOne(
    { _id: 1 },
    { $inc: { balance: -100 } },
    { session }
  );
  
  await db.collection('accounts').updateOne(
    { _id: 2 },
    { $inc: { balance: 100 } },
    { session }
  );
  
  await session.commitTransaction();
  console.log("Transaction completed successfully");
} catch (error) {
  await session.abortTransaction();
  console.error("Transaction aborted:", error);
} finally {
  await session.endSession();
}
```

### Replication

MongoDB achieves high availability through replica sets, which are groups of MongoDB instances that maintain the same data set.

**Key Concepts:**
- **Primary**: Receives all write operations
- **Secondaries**: Replicate the primary's data
- **Elections**: Automatic failover if primary becomes unavailable

### Sharding

Sharding is a method for distributing data across multiple machines. MongoDB uses sharding to support deployments with very large data sets and high throughput operations.

**Components:**
- **Shard**: Each shard contains a subset of the sharded data
- **Mongos**: Acts as a query router
- **Config Servers**: Store metadata and configuration settings

### Performance Optimization

1. **Index Optimization**
   - Create indexes to support your queries
   - Use compound indexes for queries on multiple fields
   - Use covered queries when possible

2. **Query Optimization**
   - Use projection to return only necessary fields
   - Limit the number of documents to scan
   - Use `$hint` to force a specific index

3. **Schema Design**
   - Consider read/write patterns
   - Use appropriate data types
   - Denormalize when reads are more frequent than writes

## 🔒 Security

### Authentication

Enable authentication in `mongod.conf`:

```yaml
security:
  authorization: enabled
```

Create a user with appropriate roles:

```javascript
use admin
db.createUser({
  user: "adminUser",
  pwd: "securePassword123",
  roles: [
    { role: "userAdminAnyDatabase", db: "admin" },
    { role: "readWriteAnyDatabase", db: "admin" }
  ]
});
```

### Authorization

MongoDB provides built-in roles:
- `read` - Read-only access
- `readWrite` - Read and write access
- `dbAdmin` - Database administration
- `userAdmin` - User management
- `clusterAdmin` - Cluster administration

### Encryption

1. **Encryption at Rest**
   - WiredTiger Storage Engine encryption
   - Filesystem-level encryption

2. **Encryption in Transit**
   - TLS/SSL for network encryption
   - Enable in `mongod.conf`:
     ```yaml
     net:
       ssl:
         mode: requireTLS
         PEMKeyFile: /path/to/mongodb.pem
     ```

## 🧪 Testing

1. **Unit Testing**
   - Use `mongodb-memory-server` for in-memory MongoDB instances
   - Example with Jest:
     ```javascript
     const { MongoMemoryServer } = require('mongodb-memory-server');
     const mongoose = require('mongoose');
     
     let mongoServer;
     
     beforeAll(async () => {
       mongoServer = await MongoMemoryServer.create();
       const mongoUri = mongoServer.getUri();
       await mongoose.connect(mongoUri);
     });
     
     afterAll(async () => {
       await mongoose.disconnect();
       await mongoServer.stop();
     });
     ```

2. **Integration Testing**
   - Test against a real MongoDB instance
   - Use a separate test database
   - Clean up test data after each test

## 🚀 Deployment

### Production Best Practices

1. **Configuration**
   - Disable telemetry
   - Enable access control
   - Configure appropriate bind IP

2. **Monitoring**
   - Enable MongoDB Cloud Manager or Ops Manager
   - Set up alerts for critical metrics
   - Monitor slow queries

3. **Backup**
   - Regular database dumps using `mongodump`
   - Enable Oplog for point-in-time recovery
   - Test backup restoration process

### Deployment Options

1. **Self-Hosted**
   - On-premises servers
   - Virtual machines
   - Containers (Docker, Kubernetes)

2. **MongoDB Atlas**
   - Fully managed database as a service
   - Automated backups and scaling
   - Global clusters

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