## Indexes

Most MySQL indexes (PRIMARY KEY, UNIQUE, INDEX, and FULLTEXT) are stored in B-trees one.  
Two or more indexes can be used together it depends on DB forecast (how many rows and other)  
To force MySQL to use or ignore an index listed in the possible_keys column, use FORCE INDEX, USE INDEX, or IGNORE
INDEX  
MySQL requires that foreign key columns be indexed; if you create a table with a foreign key constraint but no index on
a given column, an index is created.
Below is B-Tree Index Characteristics

No index used

```sql
/* index_part1 is not used */
... WHERE index_part2=1 AND index_part3=2

/*  Index is not used in both parts of the WHERE clause  */
... WHERE index=1 OR A=10

/* No index spans all rows  */
... WHERE index_part1=1 OR index_part2=10

```

### Index types

- clustered index (for innoDB. In MyIsam index and data store separate)
- non-clustered indexes or secondary indexes

A cluster index is the table itself, which enforces the order of the rows in the table.
An InnoDB table always has a clustered index.

Table with info about indexes ```innodb_index_stats```.  
Size of primary index is size of table itself.  
innodb_index_stats.stat_name * @@innodb_page_size (16384 bit) = index_size  
Each secondary index contain primary index.

### Composite indexes

- The first part of the index should be the column most used.
- If you always use many columns when selecting from the table, the first column in the index should be the one with the
  most duplicates, to obtain better compression of the index.
- up to 16 columns
- As an alternative to a composite index, you can introduce a column that is “hashed” based on information from other
  columns.

if you have a three-column index on (col1, col2, col3), you have indexed search capabilities on (col1), (col1, col2),
and (col1, col2, col3)

### covering index

If we take only column that all present in index that data can be extracted from index directly.

```sql
SELECT x, z
FROM tbl
WHERE y = 5
  AND q = 7; # INDEX(y,q,x,z)
```

Examples

cost 12090; time 14.8

```sql
select role_id, status, entity_id
from user
         USE INDEX (roleId_status_entityId);
```

Each secondary index contain primary key (clustered index).
So to use covering index select may contain id.  
Primary key (clustered index) in explain plan in Extra column we see Using index

```sql
select role_id, status, entity_id, id
from user
         USE INDEX (roleId_status_entityId);
```

cost 12090; time 26.7

```sql
select role_id, status, entity_id
from user
         USE INDEX ();
```

### Index in order by (Sorting)

When DB can get all info from index then index can be used.  
In that cases FULL_INDEX_SCAN used to sort

```sql
select address_id
from user
order by address_id;
```

But not here when we add more fields in select (Table scan used)

```sql
select address_id, role_id
from user
order by address_id;
```

Index can be used

```sql
SELECT a
FROM t1
ORDER BY a;

SELECT ABS(a) AS b
FROM t1
ORDER BY a;
```

Can't be

```sql
SELECT ABS(a) AS a
FROM t1
ORDER BY a;
```

#### Invisible Indexes

Indexes are visible by default. To control visibility explicitly use a VISIBLE or INVISIBLE keyword.
Invisible index don't used in read operations, but still maintained for write and update operations.
Can be used to test DB index performance.

#### Descending Indexes

```sql
CREATE TABLE t
(
    c1 INT,
    c2 INT,
    INDEX idx1 (c1 ASC, c2 ASC),
    INDEX idx2 (c1 ASC, c2 DESC),
    INDEX idx3 (c1 DESC, c2 ASC),
    INDEX idx4 (c1 DESC, c2 DESC)
);

ORDER BY c1 ASC, c2 ASC;    -- optimizer can use idx1
ORDER BY c1 DESC, c2 DESC; -- optimizer can use idx4
ORDER BY c1 ASC, c2 DESC; -- optimizer can use idx2
ORDER BY c1 DESC, c2 ASC; -- optimizer can use idx3
```

#### Index with LIKE

```sql
SELECT *
FROM tbl_name
WHERE key_col LIKE 'Patrick%'; -- index can be used
SELECT *
FROM tbl_name
WHERE key_col LIKE 'Pat%_ck%'; -- index can be used

SELECT *
FROM tbl_name
WHERE key_col LIKE '%Patrick%'; -- no index
SELECT *
FROM tbl_name
WHERE key_col LIKE other_col; -- no index
```

### Index tips

#### Secondary index (one column)

- The right question to ask is which column will reduce the result set the most.  
  Choose most selective index, column with most different values

#### Composite index

- Columns with equality conditions should go first in index.
- If you have multiple columns with equalities in your query, placing the one with the fewest different values first
  will give the best compression advantage
- When you have a composite index that could be in any order, the cardinality of the individual columns does not matter
  in picking the order. The cardinality of the entire index is what matters.
- In some cases it may be worth including a column in an index if this means all the columns in your query are in the
  index. This enables an index only scan, so you don't need to access the table.
- column order in where part doesn't matter
- When using range (<,>, beetween, is not null), rest of the index can't be used

```sql
select *
from `order`
         USE INDEX (external_id_delivery_id_status)
where status = 40
  and delivery_id = 102281
  and external_id is not null;
# Index not used as external_id column goes first in the index and it used as range. 
# And at this index the most helpful (selective) column is delivery_id

select *
from `order`
         USE INDEX (status_delivery_id_external_id)
where status = 40
  and delivery_id = 102281
  and external_id is not null;
# Key is used
```

### Secondary index types

- Spatial Indexes (for Coordinate, Point etc.)
- Multi-Valued Indexes (JSON arrays)
- Full-Text Indexes
- Unique
- Column Prefix Key Parts
- Functional Key Parts (usual index doesn't work with function)

```sql 
CREATE TABLE t1
(
    col1 INT,
    col2 INT,
    INDEX func_index ((ABS(col1)))
);
CREATE INDEX idx1 ON t1 ((col1 + col2));
CREATE INDEX idx2 ON t1 ((col1 + col2), (col1 - col2), col1);
ALTER TABLE t1
    ADD INDEX ((col1 * 40) DESC);
```

### Can MySQL use multiple indexes for a single query?

- Each SELECT in a query -- think UNION, subquery, derived table, etc -- is optimized separately. That is each might use
  different indexes.
- One SELECT can use multiple indexes in what it calls "index merge". This is rarely used.
- When the EXPLAIN says it is using Index merge (intersect), then it can almost always be improved by using a composite
  index containing the columns used by the intersected indexes.
- Index merge (union) is sometimes (rarely) used for an OR. Such can perhaps be improved by rewriting the query to be
  the UNION of two SELECTs.

index merge example

```sql
# Intersection Access Algorithm
SELECT *
FROM tbl_name
WHERE key1_part1 = 1
  AND key1_part2 = 2
  AND key2 = 2;
# Union Access Algorithm
SELECT *
FROM t1
WHERE key1 = 1
   OR key2 = 2
   OR key3 = 3;
```

### Many-to-Many Mapping table

```sql
    CREATE TABLE XtoY (
        # No surrogate id for this table
        x_id MEDIUMINT UNSIGNED NOT NULL,   -- For JOINing to one table
        y_id MEDIUMINT UNSIGNED NOT NULL,   -- For JOINing to the other table
        # Include other fields specific to the 'relation'
        PRIMARY KEY(x_id, y_id),            -- When starting with X
        INDEX      (y_id, x_id)             -- When starting with Y
    ) ENGINE=InnoDB;
```

### Tips

- Flags, and other fields with few values ("low cardinality"), should not be alone in an index -- the index won't be
  used.
- It is ok to have Index_length > Data_length.
- 5 columns in a compound index seems "too many"
- INDEX(a,b) covers for INDEX(a), so drop the latter.
- 2 out of 3 tables have a perfectly good "natural" PRIMARY KEY, so AUTO_INCREMENT is not necessary.
- A FULLTEXT index will be used before any other index.
- If more than 20% of the rows are needed, a table scan is faster than using the obvious INDEX. The 20% is
  approximate -- 10%-30% seems to be the typical range for the cutoff between the two algorithms
- Limit the number of indexes on a table to about 6 (Rule of Thumb). 

