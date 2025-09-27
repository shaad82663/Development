# MongoDB and PostgreSQL Architecture Notes

---

## MongoDB Architecture Notes

- **What is MongoDB?**
  - MongoDB is a NoSQL database that doesn’t use traditional relationships like SQL databases.
  - It stores data in a document-oriented model using BSON (Binary JSON) format.

- **Key Concepts:**
  - Uses **collections** (similar to tables in SQL) to store data.
  - Data is flexible and nested, making it ideal for modern applications.

- **How Data is Stored on Disk:**
  - Follows a Unix file system with a tree-like structure.
  - Files are stored in **blocks** on disk, managed by **inodes** (data structures with file info like size and permissions).
  - A **block map** tracks used and free disk blocks.

- **How MongoDB Works:**
  - Uses a **memory-mapped file system** (with MMAPv1) or a cache (with WiredTiger) to store data on disk and map it to memory for faster access.
  - Only a portion of data is kept in memory based on demand; the rest stays on disk until needed.
  - Avoids slow disk reads by prioritizing frequently accessed data in memory or cache.

- **Document Lifecycle:**
  1. **Document Validation**: Checks document structure (e.g., required fields like title, author, body) using JSON schema.
     - Example: Rejects documents missing required fields.
  2. **Document Storage**: Stores validated data as binary on disk using `insertOne`.
  3. **Index Creation**: Builds indexes (e.g., on `title`) using B-trees for fast queries.
  4. **Memory Allocation**: Allocates memory or cache space for quick access without disk reads.
  5. **Document Retrieval**: Checks memory or cache first; reads from disk if not found.

- **Storage Engine:**
  - Default is **WiredTiger**, offering better performance than the older MMAPv1.
  - Features:
    - Document-level concurrency control.
    - Multi-document transactions for complex updates.
    - Data compression and caching to save space and speed up access.
    - B-tree indexing for efficient searches.
    - Point-in-time recovery and hot backups.
    - Supports encryption at rest for security.

- **Benefits Over SQL:**
  - **Flexible Schema**: Adapts to changing data structures.
  - **Scalability**: Scales horizontally with sharding across multiple nodes.
  - **High Performance**: Uses in-memory caching or memory mapping for fast reads/writes.
  - **Strong Consistency**: Ensures data consistency with multi-document transactions.
  - **Rich Querying**: Supports advanced queries, aggregations, and geospatial indexing.
  - **Efficient Resources**: Uses BSON for smaller data sizes and supports column-store indexes for analytics.
  - **Change Streams**: Allows real-time data updates for applications.

---

## PostgreSQL Architecture Notes

- **What is PostgreSQL?**
  - An open-source, enterprise-grade relational database with over 30 years of development.
  - Supports SQL and handles transactional/analytical workloads.

- **Why Use PostgreSQL?**
  - Offers features for performance, security, and extensibility.
  - Supports multiple languages (SQL, Python, Java, etc.) for functions.
  - Provides diverse data types (JSONB, arrays, geometric types) and custom types.
  - Includes full-text search, authentication, and foreign data wrappers.
  - Supports logical replication for high availability.

- **Basic Architecture:**
  - Follows a **client-server model**.
  - Master process (Postmaster) forks a new process for each client connection.
  - Limited by CPU cores and RAM; uses **connection pooling** (e.g., PgBouncer) to manage excess requests.
  - Supports parallel query execution for faster processing.

- **Process Types:**
  1. **PostgreSQL Server Process (Postmaster)**:
     - Manages client connections and starts backend processes.
  2. **Backend Process**:
     - Handles queries and transactions for each client.
  3. **Background Worker Process**:
     - Performs maintenance tasks (e.g., vacuuming, auto-analyze) without user connection.

- **Memory Management:**
  - **Local Memory**: Used by backend processes for queries.
  - **Shared Memory**: Used by the server process for inter-process communication.
    - **Shared Buffers**: Cache for IO operations, reducing disk reads.
    - **WAL Buffers**: Store transaction logs for recovery and performance.
    - **Work Memory**: Allocates memory for query execution, adjustable via `work_mem`.

- **Database Structure:**
  - **Logical Structure**:
    - **Cluster**: Collection of databases on one server.
    - **Database**: Contains schemas (logical groups of tables, views, indexes).
    - **Catalog Tables**: Store metadata about database objects.
    - **Tables**: Rows and columns for data storage.
    - **Indexes**: Speed up data retrieval with B-tree, GiST, or BRIN options.
    - **Views**: Virtual tables for simplified queries.
    - **Constraints**: Enforce data integrity (e.g., uniqueness, foreign keys).
  - **Physical Structure**:
    - Each database has its own directory.
    - Tables are stored in **heap files** (1 GB default, 8 KB pages).
    - Data spans multiple files when full.
    - Supports table partitioning for large datasets.

---