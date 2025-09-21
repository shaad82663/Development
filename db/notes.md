# Advanced Database Concepts

This guide covers advanced database concepts essential for building scalable, efficient, and reliable systems, tailored for backend developers with experience in Node.js and MySQL. It organizes and expands on provided notes, including indexing, query optimization, vacuuming, B/B+ trees, and adds key topics like transactions, sharding, replication, and stored procedures. Each section includes explanations, practical MySQL examples, and Node.js integration where relevant.

## 1. Indexing and Query Optimization

Indexes improve query performance by allowing the database to locate data faster, reducing full table scans. Proper indexing is critical for large datasets, such as your pin density queries (from May 17, 2025).

### Types of Indexes
- **Primary Index**: Automatically created for primary keys, ensuring unique identification.
- **Unique Index**: Enforces uniqueness (e.g., email addresses).
- **Composite Index**: Covers multiple columns for queries filtering on several fields.
- **Covering Index**: Includes all columns needed for a query, enabling index-only scans.
- **Spatial Index**: Optimizes geographic data queries (e.g., latitude/longitude for pin density).

### Index Scan vs. Index-Only Scan
- **Index Scan**: Uses the index to find matching rows but accesses the table (heap) for additional data.
- **Index-Only Scan**: Retrieves all required data directly from the index, avoiding heap access. Requires all queried columns to be in the index (e.g., via `INCLUDE` in MySQL 8.0+).

**Example (MySQL)**:
```sql
-- Create a regular index
CREATE INDEX g_idx ON students(grade);

-- Create a covering index
CREATE INDEX g_idx_with_id ON students(grade) INCLUDE (id);

-- Query to analyze execution
EXPLAIN ANALYZE SELECT id, grade FROM students WHERE grade = 10;
```
- **Output**: For the covering index, MySQL uses an index-only scan, avoiding heap access, which is faster.

**Cons of Index-Only Scans**:
- Indexes consume storage space.
- Write operations (INSERT/UPDATE/DELETE) are slower due to index maintenance.

### Bitmap Heap Scan
- Used when queries match many rows, reducing random I/O by creating a bitmap of matching row locations.
- More efficient than index scans for large datasets but less so for small, precise queries.

**Case Study: Table `int` (columns: a, b, c, all INTEGER)**

1. **Separate Indexes on `a` and `b`**:
```sql
CREATE INDEX idx_a ON int(a);
CREATE INDEX idx_b ON int(b);

SELECT c FROM int WHERE a = 10 AND b = 20;
```
- MySQL uses both indexes, creating bitmaps for `a=10` and `b=20`, then performs a `BITMAP AND` to find rows matching both conditions.
- For `OR` conditions (e.g., `WHERE a = 10 OR b = 20`), MySQL uses `BITMAP OR`.

2. **Composite Index on `(a, b)`**:
```sql
CREATE INDEX ab_idx ON int(a, b);

SELECT c FROM int WHERE a = 10;
```
- MySQL uses the composite index, as `a` is the leading column.
```sql
SELECT c FROM int WHERE b = 10;
```
- MySQL cannot use the composite index, as `b` is not the leading column; it falls back to a full table scan or another index on `b` if available.
```sql
SELECT c FROM int WHERE b = 10 AND a = 20;
```
- MySQL uses the composite index, leveraging both columns.
```sql
SELECT c FROM int WHERE b = 10 OR a = 20;
```
- MySQL may perform a full table scan if only the composite index exists, as `OR` conditions don’t align with the index structure.

### Query Execution Decision
- **Factors**: Table size, number of matching rows, index selectivity, and query complexity.
- **Index Scan**: Used for small, selective queries (e.g., fetching a few rows).
- **Bitmap Heap Scan**: Used for queries matching many rows, minimizing I/O.
- **Full Table Scan**: Used when no suitable index exists or all rows are needed.

**Example (Node.js with MySQL)**:
```javascript
const mysql = require('mysql2/promise');
const pool = mysql.createPool({ host: 'localhost', user: 'root', database: 'test' });

async function queryStudents() {
  const [rows] = await pool.query('EXPLAIN ANALYZE SELECT id, grade FROM students WHERE grade = 10');
  console.log('Query Plan:', rows);
}
queryStudents();
```

### Creating Indexes in Production
- **MySQL**: Index creation locks the table, blocking writes. Use smaller transactions or create indexes during low-traffic periods.
```sql
CREATE INDEX idx_grade ON students(grade);
```
- **PostgreSQL (for comparison)**: Supports `CREATE INDEX CONCURRENTLY`, allowing writes during index creation but using more resources.
```sql
CREATE INDEX CONCURRENTLY idx_grade ON students(grade);
```

## 2. Vacuuming and Database Maintenance

Vacuuming reclaims space from deleted or updated rows (dead tuples) and optimizes performance.

- **MySQL**: Uses `OPTIMIZE TABLE` to reclaim space and defragment tables (InnoDB engine).
```sql
OPTIMIZE TABLE students;
```
- **PostgreSQL**: Uses `VACUUM` to remove dead tuples and update statistics.
```sql
VACUUM VERBOSE students;
```
- **Why It’s Needed**: Updates and deletes create dead tuples, wasting space and slowing queries. Vacuuming compacts data and updates optimizer statistics.
- **Node.js Integration**:
```javascript
async function optimizeTable() {
  const [result] = await pool.query('OPTIMIZE TABLE students');
  console.log('Optimization Result:', result);
}
optimizeTable();
```

## 3. B-Trees and B+ Trees

Indexes in databases like MySQL and PostgreSQL often use B-trees or B+ trees for efficient data retrieval.

- **Tuple ID (TID)**: In PostgreSQL, each row has a TID (block number + offset), pointing to its location in the heap. MySQL uses similar internal pointers.
- **B-Tree**:
  - Stores keys and values in all nodes.
  - Larger size due to storing full data.
  - Suitable for equality queries.
- **B+ Tree** (used by MySQL InnoDB):
  - Stores keys in internal nodes, values in leaf nodes.
  - Leaf nodes are linked, enabling efficient range queries.
  - More space-efficient and faster for both equality and range queries.

**Example**: For a table `students(grade)`, a B+ tree index on `grade` allows fast lookups for `WHERE grade = 10` and range queries like `WHERE grade BETWEEN 8 AND 12`.

## 4. Transactions and ACID Properties

Transactions ensure data integrity for operations involving multiple steps.

- **ACID**:
  - **Atomicity**: All operations succeed or fail together.
  - **Consistency**: Database remains in a valid state.
  - **Isolation**: Transactions are independent, preventing partial visibility.
  - **Durability**: Committed changes are permanent.
- **MySQL Example**:
```sql
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;
COMMIT;
```
- **Node.js Integration**:
```javascript
async function transferMoney() {
  const connection = await pool.getConnection();
  try {
    await connection.beginTransaction();
    await connection.query('UPDATE accounts SET balance = balance - 100 WHERE user_id = 1');
    await connection.query('UPDATE accounts SET balance = balance + 100 WHERE user_id = 2');
    await connection.commit();
    console.log('Transfer successful');
  } catch (error) {
    await connection.rollback();
    console.error('Transfer failed:', error);
  } finally {
    connection.release();
  }
}
transferMoney();
```

## 5. Sharding and Replication

For scalability, especially in distributed systems like your pin density application, sharding and replication are key.

- **Sharding**:
  - Splits data across multiple servers based on a key (e.g., user_id).
  - Improves performance for large datasets but complicates queries.
  - **Example**: Shard `pins` table by `region_id` for geographic data.
- **Replication**:
  - Copies data to multiple servers for redundancy and read scalability.
  - **Primary-Replica**: Write to primary, read from replicas.
  - **MySQL Example**:
```sql
-- Configure replica in my.cnf or my.ini
[mysqld]
server-id=2
read_only=1
```
- **Node.js Integration**:
```javascript
const primaryPool = mysql.createPool({ host: 'primary-db', user: 'root', database: 'pins' });
const replicaPool = mysql.createPool({ host: 'replica-db', user: 'root', database: 'pins' });

async function getPinDensity() {
  const [rows] = await replicaPool.query('SELECT lat, lon FROM pins WHERE region_id = ?', [1]);
  return rows;
}
```

## 6. Stored Procedures and Triggers

Stored procedures and triggers automate complex logic in the database.

- **Stored Procedure**:
```sql
DELIMITER //
CREATE PROCEDURE UpdateStudentGrade(IN studentId INT, IN newGrade INT)
BEGIN
  UPDATE students SET grade = newGrade WHERE id = studentId;
END //
DELIMITER ;
```
- **Node.js Call**:
```javascript
async function updateGrade(studentId, newGrade) {
  const [result] = await pool.query('CALL UpdateStudentGrade(?, ?)', [studentId, newGrade]);
  console.log('Grade updated:', result);
}
```
- **Trigger**:
```sql
CREATE TRIGGER after_student_update
AFTER UPDATE ON students
FOR EACH ROW
INSERT INTO audit_log (student_id, old_grade, new_grade)
VALUES (OLD.id, OLD.grade, NEW.grade);
```

## 7. Query Optimization Tips
- **Use EXPLAIN ANALYZE**: Understand query plans to identify bottlenecks.
- **Avoid SELECT***: Specify only needed columns.
- **Use Joins Efficiently**: Prefer indexed columns in JOIN conditions.
- **Batch Updates**: Reduce transaction overhead for bulk operations.
- **Spatial Indexes**: For pin density queries, use MySQL’s `SPATIAL INDEX` for `lat`/`lon`.

**Example**:
```sql
CREATE SPATIAL INDEX sp_idx ON pins(location);
SELECT * FROM pins WHERE ST_Distance_Sphere(location, POINT(40.7128, -74.0060)) < 500;
```

## Further Reading
- [MySQL Documentation: Indexing](https://dev.mysql.com/doc/refman/8.0/en/optimization-indexes.html)
- [PostgreSQL: VACUUM](https://www.postgresql.org/docs/current/sql-vacuum.html)
- [B+ Trees in Databases](https://use-the-index-luke.com/sql/anatomy/b-tree-vs-b-plus-tree)