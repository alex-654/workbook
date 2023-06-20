
### Group by date DATE(), month MONTH(), hour HOUR() and so on
List on functions - 
https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html
```sql
select DATE(created_at) as date, count(id)
from order
group by date;

select schedule_template_id, HOUR(date) as hour_part, avg(orders_count)
from user_schedule_load
group by schedule_template_id, hour_part
order by schedule_template_id, hour_part;
```

get Timestamp diff in seconds
```sql
SELECT TIMESTAMPDIFF(SECOND, NOW(), `expired_at`) from table_name
```

### CTE example
```sql
WITH ARRIVED AS (SELECT `order_id`, `manager_id`, `created_at`
                 FROM `order_history`
                 WHERE (`order_history`.`manager_type` = 'UserKey')
                   AND (order_history.order_point_id is null)
                   AND (`order_history`.`field` = 'status')
                   AND (`order_history`.`new_value` = '20')
                   AND (order_history.created_at between '2023-02-20' and '2023-02-28')),
    LEAVED AS (SELECT `order_id`, `manager_id`, `created_at`
               FROM `order_history`
               WHERE (`order_history`.`manager_type` = 'UserKey')
                 AND (order_history.order_point_id is null)
                 AND (`order_history`.`field` = 'status')
                 AND (`order_history`.`new_value` = '30')
                 AND (order_history.created_at between '2023-02-20' and '2023-02-28'))
SELECT ARRIVED.order_id, ARRIVED.manager_id
from ARRIVED
inner join LEAVED on ARRIVED.order_id = LEAVED.order_id and ARRIVED.manager_id = LEAVED.manager_id
```
## Indexes
Most MySQL indexes (PRIMARY KEY, UNIQUE, INDEX, and FULLTEXT) are stored in B-trees one.  
Two or more indexes can be used together it depends on DB forecast (how many rows and other)  
To force MySQL to use or ignore an index listed in the possible_keys column, use FORCE INDEX, USE INDEX, or IGNORE INDEX  
Below is B-Tree Index Characteristics

If it is very likely that a long string column has a unique prefix on the first number of characters, it is better to index only this prefix

No index used
```sql
select *
from user
where phone = '79850796866' or konsole_pro_cabinet_id = 1;

/* index_part1 is not used */
... WHERE index_part2=1 AND index_part3=2

    /*  Index is not used in both parts of the WHERE clause  */
... WHERE index=1 OR A=10

    /* No index spans all rows  */
... WHERE index_part1=1 OR index_part2=10

```

Index used
```sql
select *
from user
where phone = '79850796866' or phone = '79586301569' and konsole_pro_cabinet_id = 1
```

### Composite indexes
- The first part of the index should be the column most used. 
- If you always use many columns when selecting from the table, the first column in the index should be the one with the most duplicates, to obtain better compression of the index.
- up to 16 columns
- As an alternative to a composite index, you can introduce a column that is “hashed” based on information from other columns.

if you have a three-column index on (col1, col2, col3), you have indexed search capabilities on (col1), (col1, col2), and (col1, col2, col3)

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

- Second field from 3 or second and third. Index not used (Full Scan cost = 12 090)
```sql
select *
from user
         USE INDEX (roleId_status_entityId)
where status = 1;
```

### covering index
If we take only column that all present in index that data can be extracted from index directly  
cost 12090; time 14.8
```sql
select role_id, status, entity_id
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
select * from user
order by id; # (primary id a bit different from other indexes)
```
 
```sql
select address_id from user
order by address_id;
```

But not here when we add more fields in select (Table scan used)
```sql
select address_id, role_id from user
order by address_id;
```

Index can be used
```sql
SELECT a FROM t1 ORDER BY a;
SELECT ABS(a) AS b FROM t1 ORDER BY a;
```

Can't be
```sql
SELECT ABS(a) AS a FROM t1 ORDER BY a;
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
ORDER BY c1 DESC, c2 DESC; -- optimizer can use idx4
ORDER BY c1 ASC, c2 DESC; -- optimizer can use idx2
ORDER BY c1 DESC, c2 ASC; -- optimizer can use idx3
```

#### Index with LIKE
```sql
SELECT * FROM tbl_name WHERE key_col LIKE 'Patrick%'; -- index can be used
SELECT * FROM tbl_name WHERE key_col LIKE 'Pat%_ck%'; -- index can be used

SELECT * FROM tbl_name WHERE key_col LIKE '%Patrick%'; -- no index
SELECT * FROM tbl_name WHERE key_col LIKE other_col; -- no index
```

### Base rules for DB
- keep tables small
- avoid null
- use the most efficient (smallest) data types possible
- use indexes

### Explain
raw format columns 
- Type. if value = ALL it's mean full table scan.
- Rows - estimate of rows to be examined
- Filtered. Percentage of rows filtered by table condition max value 100% which means no filtering of rows occurred
- Extra. This column contains additional information about how MySQL resolves the query

### How to keep money in DB
In DB use decimal with precision you need.
In php use math functions like https://www.php.net/manual/en/ref.bc.php or library.