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
SELECT TIMESTAMPDIFF(SECOND, NOW(), `expired_at`)
from table_name
```

### CTE (common table expression) example

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

### CTE with window function

solution for https://www.codewars.com/kata/649563164b1bbf004be34cd6/sql

```sql
with score as (select student_id, sum(score) as total_score
               from courses
               where course_name in ('Math', 'Science')
               group by student_id),
     my_rank as (select *
                 from students
                          inner join score on score.student_id = students.id)

select rank() over (order by total_score desc, student_id asc) as rank,
student_id, name, total_score
from my_rank
```

### Recursive CTE

solution for https://www.codewars.com/kata/6486fc7924f768003c0b0f31/sql  
recursive explain img https://www.sqlservertutorial.net/wp-content/uploads/SQL-Server-Recursive-CTE-execution-flow.png

```sql
with recursive rec_cte as (select e.id, e.name, '' as management_chain
                           from employees as e
                           where e.manager_id is null

                           union all

                           select e.id,
                                  e.name,
                                  case
                                      when rec_cte.management_chain = '' then rec_cte.name || ' (' || rec_cte.id || ')'
                                      else
                                          rec_cte.management_chain || ' -> ' || rec_cte.name || ' (' ||
                                          rec_cte.id || ')'
                                      end as management_chain
                           from employees as e
                                    inner join rec_cte on rec_cte.id = e.manager_id)
select id, name, management_chain
from rec_cte
order by rec_cte.id;
```

### Window function example

good tutorial https://learnsql.com/blog/sql-window-functions-rows-clause/
solution for https://www.codewars.com/kata/6035b3c78e0085002231092b/sql

```sql
with agregate_by_day_operations as (select DATE(date) as date, sum(amount) as amount
                                    from operations
                                    group by DATE(date)
                                    order by date)

select date, sum(amount) over (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as balance
from agregate_by_day_operations;
```

### Base rules for DB

- keep tables small
- avoid null
- use the most efficient (smallest) data types possible
- use indexes but not to much
- the more diverse the data, the more effective index

### Explain

raw format columns

- Type. if value = ALL it's mean full table scan.
- Rows - estimate of rows to be examined
- Filtered. Percentage of rows filtered by table condition max value 100% which means no filtering of rows occurred
- Extra. This column contains additional information about how MySQL resolves the query

### How to keep money in DB

In DB use decimal with precision you need.
In php use math functions like https://www.php.net/manual/en/ref.bc.php or library.

### Foreign keys

- DB rejects any INSERT or UPDATE operation that attempts to create a foreign key value in a child table if there is no
  matching candidate key value in the parent table.
- MySQL requires that foreign key columns be indexed;
- if you create a table with a foreign key constraint but no index on a given column, an index is created.
- Foreign key name must be unique in the database
- Parent table column and child table column must be the same type
- Referential Actions (ON UPDATE | ON DELETE) values:
    - CASCADE
      Delete or update the row from the parent table and automatically delete or update the matching rows in the child
      table.  
      ON UPDATE CASCADE make sense when parent key ist not primary_key and can be changed
    - RESTRICT==NO ACTION (default) Rejects the delete or update operation for the parent table.
    - SET NULL