# MongoDB and PostgreSQL Architecture Notes for Interview Preparation

This Markdown file provides a simple, pointwise breakdown of MongoDB and PostgreSQL architecture notes, focusing on important concepts for interview preparation. Written in easy language, it covers key points to help you understand and explain these databases effectively. Last updated: 10:28 AM IST on Saturday, September 27, 2025.

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
  - Uses a **memory-mapped file system** to store data on disk and map it to memory for faster access.
  - Avoids slow disk reads by keeping data in memory when possible.

- **Document Lifecycle:**
  1. **Document Validation**: Checks document structure (e.g., required fields like title, author, body) using JSON schema.
     - Example: Rejects documents missing required fields.
  2. **Document Storage**: Stores validated data as binary on disk using `insertOne`.
  3. **Index Creation**: Builds indexes (e.g., on `title`) using B-trees for fast queries.
  4. **Memory Allocation**: Allocates memory for quick access without disk reads.
  5. **Document Retrieval**: Checks memory first; reads from disk if not found.

- **Storage Engine:**
  - Default is **WiredTiger**, offering better performance than MMAPv1.
  - Features:
    - Document-level concurrency control.
    - Transactions for multiple updates.
    - Data compression and caching.
    - B-tree indexing for efficiency.
    - Point-in-time recovery and hot backups.

- **Benefits Over SQL:**
  - **Flexible Schema**: Adapts to changing data structures.
  - **Scalability**: Scales horizontally with more nodes.
  - **High Performance**: Uses in-memory data for fast reads/writes.
  - **Strong Consistency**: Ensures data consistency across nodes.
  - **Rich Querying**: Supports indexing and flexible queries.
  - **Efficient Resources**: Uses BSON for smaller data sizes.

- **Interview Tip**: Highlight MongoDB’s flexibility and scalability for dynamic apps, and mention WiredTiger’s role in performance.

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

- **Basic Architecture:**
  - Follows a **client-server model**.
  - Master process (Postmaster) forks a new process for each client connection.
  - Limited by CPU cores and RAM; uses **connection pooling** to manage excess requests.

- **Process Types:**
  1. **PostgreSQL Server Process (Postmaster)**:
     - Manages client connections and starts backend processes.
  2. **Backend Process**:
     - Handles queries and transactions for each client.
  3. **Background Worker Process**:
     - Performs maintenance tasks (e.g., vacuuming) without user connection.

- **Memory Management:**
  - **Local Memory**: Used by backend processes for queries.
  - **Shared Memory**: Used by the server process for inter-process communication.
    - **Shared Buffers**: Cache for IO operations, reducing disk reads.
    - **WAL Buffers**: Store transaction logs for recovery and performance.

- **Database Structure:**
  - **Logical Structure**:
    - **Cluster**: Collection of databases on one server.
    - **Database**: Contains schemas (logical groups of tables, views, indexes).
    - **Catalog Tables**: Store metadata about database objects.
    - **Tables**: Rows and columns for data storage.
    - **Indexes**: Speed up data retrieval.
    - **Views**: Virtual tables for simplified queries.
    - **Constraints**: Enforce data integrity (e.g., uniqueness).
  - **Physical Structure**:
    - Each database has its own directory.
    - Tables are stored in **heap files** (1 GB default, 8 KB pages).
    - Data spans multiple files when full.

- **Interview Tip**: Emphasize PostgreSQL’s robustness, concurrency, and extensibility, and explain how shared buffers and WAL improve performance.

---

### Final Notes
- **MongoDB**: Great for flexible, scalable apps; focus on NoSQL benefits and WiredTiger.
- **PostgreSQL**: Ideal for structured data and enterprise needs; highlight client-server model and memory management.
- Practice explaining these points with examples (e.g., document validation, connection pooling) to stand out in interviews!