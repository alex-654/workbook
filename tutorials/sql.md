
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
One, Two or more indexes can be used together it depends on DB forecast (how many rows and other)

### Composite indexes
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
When DB can get all info from index then index can be used
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
order by entity_id, status
```
Full scan
```sql
select role_id, status, entity_id, address_id
from user
         USE INDEX (roleId_status_entityId)
order by entity_id
```

### How to keep money in DB
In DB use decimal with precision you need.
In php use math functions like https://www.php.net/manual/en/ref.bc.php or library.