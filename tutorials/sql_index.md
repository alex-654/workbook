## Indexes

Most MySQL indexes (PRIMARY KEY, UNIQUE, INDEX, and FULLTEXT) are stored in B-trees one.  
Two or more indexes can be used together it depends on DB forecast (how many rows and other)  
To force MySQL to use or ignore an index listed in the possible_keys column, use FORCE INDEX, USE INDEX, or IGNORE
INDEX  
Below is B-Tree Index Characteristics

If it is very likely that a long string column has a unique prefix on the first number of characters, it is better to
index only this prefix

No index used

```sql
/* index_part1 is not used */
.
.. WHERE index_part2=1 AND index_part3=2

/*  Index is not used in both parts of the WHERE clause  */
.
.. WHERE index=1 OR A=10

/* No index spans all rows  */
.
.. WHERE index_part1=1 OR index_part2=10

```

### Index types

- clustered index
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

- All fields in index included in where (UNIQUE_INDEX_SCAN cost = 24)

```sql
select *
from user
         USE INDEX (roleId_status_entityId)
where role_id = 0
  and status = 1
  and entity_id = 600;
```

- Two fields from 3 (UNIQUE_INDEX_SCAN cost = 162)

```sql
select *
from user
         USE INDEX (roleId_status_entityId)
where role_id = 0
  and status = 1;
```

- First fields from 3 (UNIQUE_INDEX_SCAN cost = 6 500)

```sql
select *
from user
         USE INDEX (roleId_status_entityId)
where role_id = 0;
```

if we have one more column index for role_id. Cost is same, but speed a bit faster. In may example its roughly 5%
May be it now worth it to maintain both indexes

```sql
select *
from user
#          USE INDEX (roleId_status_entityId)
         USE INDEX (users_role_id)
where role_id = 0
```

- Second field from 3 or second and third. Index not used (Full Scan cost = 12 090)

```sql
select *
from user
         USE INDEX (roleId_status_entityId)
where status = 1;
```

-

```sql
select *
from user
         USE INDEX (roleId_status_entityId)
where role_id = 0
```

### covering index

If we take only column that all present in index that data can be extracted from index directly.

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
select *
from user
order by id; # (primary id a bit different from other indexes)
```

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

#### Complex index

Index Scan

```sql
select role_id, status, entity_id
from user
         USE INDEX (roleId_status_entityId)
order by entity_id, status # possibly covering index used
```

Full scan

```sql
select role_id, status, entity_id, address_id
from user
         USE INDEX (roleId_status_entityId)
order by entity_id
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
ORDER BY c1
DESC, c2 DESC; -- optimizer can use idx4
ORDER BY c1 ASC, c2
DESC; -- optimizer can use idx2
ORDER BY c1
DESC, c2 ASC; -- optimizer can use idx3
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
- In some cases it may be worth including a column in an index if this means all the columns in your query are in the
  index. This enables an index only scan, so you don't need to access the table.

### secondary index types

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