# MongoDB Internal Architecture

Database systems share core fundamentals at their storage layer, enabling objective comparisons across different DBMS. For instance, MongoDB’s document storage is fundamentally similar to how MySQL or PostgreSQL store rows. The key is efficient data retrieval from disk with minimal I/O operations. The rest is API.

This article explores the evolution of MongoDB’s internal architecture, focusing on document storage, retrieval, and index representation. It assumes familiarity with database engineering concepts like indexes, B+Trees, data files, and Write-Ahead Logging (WAL).

## Components of MongoDB

### Documents & Collections
MongoDB is a document-based NoSQL database, handling schema-less JSON documents stored internally as BSON (Binary JSON) for efficient storage and traversal. BSON encodes type and length information, making it faster to process than JSON. Documents can be compressed to reduce size, a feature introduced in later versions.

Collections in MongoDB are analogous to tables in RDBMS but are schema-less, allowing documents with varying fields within the same collection. This flexibility attracts developers but can be misused if not carefully managed.

### _id Index
Every MongoDB collection automatically creates a primary key `_id` field, typically a 12-byte `ObjectId`, ensuring uniqueness across machines or shards for scalability. Users can override `_id` with custom values, potentially increasing its size. A B+Tree index is created on `_id`, mapping it to the BSON document for optimal search performance.

### Secondary Indexes
Users can create secondary B+Tree indexes on any field, enabling fast queries beyond `_id`. Without these, MongoDB resorts to full collection scans, which are inefficient. The size of secondary indexes depends on the indexed field’s size and the document pointer (e.g., `DiskLoc` or `recordId`), varying by storage engine and MongoDB version.

### Write-Ahead Logging (WAL)
MongoDB uses a journal for durability, logging operations before applying them to the database. This ensures crash recovery by replaying the journal to restore the database to a consistent state. WiredTiger, the default storage engine, optimizes WAL with efficient checkpointing.

## Evolution of MongoDB Architecture

### Original MongoDB Architecture (MMAPv1)
MongoDB’s initial storage engine, MMAPv1 (Memory-Mapped Files), stored uncompressed BSON documents on disk. The `_id` primary key index mapped to a `DiskLoc`, a pair of 32-bit integers (file number and offset), enabling O(1) document retrieval.

**Limitations:**
- **Offset Maintenance:** Updating a document could increase its size, shifting offsets and requiring updates to all subsequent `DiskLoc` values, impacting performance.
- **Global Lock:** MMAPv1 used a single database-level write lock, serializing writes and limiting concurrency.
- **Inefficient I/O:** Uncompressed documents increased I/O demands.

**Performance:** One index lookup plus one I/O: O(log n) + O(1).

MongoDB improved MMAPv1 to use collection-level locks but deprecated it in version 4.0 in favor of WiredTiger.

### WiredTiger Architecture
Acquired in 2014, WiredTiger became MongoDB’s default storage engine, introducing:
- **Document-Level Locking:** Allowing concurrent writes to different documents in the same collection.
- **Compression:** BSON documents are compressed, reducing I/O and storage needs.
- **Hidden Index:** Documents are stored in a hidden index with `recordId` (64-bit integer) and BSON pairs.

**Index Changes:**
- Primary and secondary indexes point to `recordId` instead of `DiskLoc`.
- Lookups require two steps: first, the primary index maps `_id` to `recordId`, then the hidden WiredTiger index maps `recordId` to the BSON document.

**Performance:** Two lookups: O(log n) + O(log n), increasing CPU, memory, and disk usage due to double index storage.

**Trade-offs:** While compression and concurrency improved, the double lookup and additional index storage could strain resources, especially for large datasets. This was a factor in some migrations (e.g., Discord’s move to Cassandra due to RAM constraints).

### Clustered Collections Architecture
Introduced in MongoDB 5.3 (June 2022), clustered collections make the `_id` index a clustered index, storing BSON documents directly in the leaf pages of the primary index, eliminating the hidden WiredTiger index.

**Benefits:**
- **Single Lookup:** Queries on `_id` return the BSON document directly: O(log n).
- **Improved Performance:** Ideal for workloads heavily using `_id`.

**Secondary Index Changes:**
- Secondary indexes now point to `_id` (12 bytes or larger if user-defined) instead of `recordId`, increasing their size.
- Lookups via secondary indexes require two steps: one to find `_id`, another to fetch the document from the primary index.

**Challenges:**
- **Larger Secondary Indexes:** Storing `_id` (12+ bytes) bloats secondary indexes, impacting memory usage and performance.
- **User-Defined `_id`:** Custom `_id` values can further increase index size, requiring careful data modeling.

**Comparison:** This architecture aligns MongoDB with MySQL’s InnoDB, where secondary indexes reference the primary key. Unlike MySQL, where tables must be clustered, MongoDB offers the choice to cluster collections.

### Advanced Internal Mechanisms
- **Cache Management:** WiredTiger uses an in-memory cache (default: 50% of RAM minus 1GB) to store frequently accessed data and indexes, reducing disk I/O. The cache employs a Least Recently Used (LRU) eviction policy.
- **Checkpointing:** WiredTiger periodically creates checkpoints, snapshots of the database state, to ensure consistency and reduce journal replay time during recovery.
- **Multi-Version Concurrency Control (MVCC):** WiredTiger supports MVCC, allowing readers to access a consistent snapshot while writers modify data, enhancing read-write concurrency.
- **Index Prefix Compression:** WiredTiger compresses index keys by storing common prefixes once, reducing index size and improving performance.
- **Sharding and Replication:** MongoDB’s `_id` design (12-byte `ObjectId`) supports sharding by ensuring unique identifiers across distributed systems. Replication uses oplogs (operation logs) stored in a special collection for consistency across replicas.

## Summary
MongoDB’s architecture has evolved from MMAPv1’s simple but limited design to WiredTiger’s efficient, concurrent model, and finally to clustered collections for optimized `_id` queries. Understanding these fundamentals—document storage, indexing, and I/O efficiency—enables predictable performance analysis across database systems.

Clustered collections are powerful but require caution due to bloated secondary indexes, especially with custom `_id` fields. For further details, refer to [MongoDB’s documentation on clustered collections](https://www.mongodb.com/docs/manual/core/clustered-collections/).

For a video explanation, check my [YouTube channel](https://www.youtube.com).