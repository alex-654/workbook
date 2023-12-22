### Transaction

- Обеспечивают атомарность. Либо все выполняется либо ничего.
- Трансзакции, обеспечивают последовательное выполнение записей, даже если они поступили в БД одновременно.
  Первая транзакция накладывает лок на данные. Вторая ждет ее выполнения или отката и потом накладывает свой лок.
  Т.е. бд в рамках транзакции получила запрос на апдейт, второй запрос на апдейт этой же записи будет ждать завершения
  первого.
- Тразакиции достпуны с разными уровнями изоляции. Эти уровни определяют какие данные может видеть текущая транзакция по
  другим незавершенным транзакциям.

### ACID (Atomicity, Consistency, Isolation, Durability)

- Atomicity guarantees that each transaction is treated as a single "unit", which either succeeds completely or fails
  completely
- Consistency ensures that a transaction can only bring the database from one consistent state to another, preserving
  database invariants:
- Isolation ensures that concurrent execution of transactions leaves the database in the same state that would have been
  obtained if the transactions were executed sequentially.
- Durability guarantees that once a transaction has been committed, it will remain committed even in the case of a
  system failure (e.g., power outage or crash).

### Isolation level

- READ UNCOMMITTED - you can read uncommitted changes by another transaction (dirty read)
- READ COMMITTED - Isolation level that guarantees that any data read is committed at the moment it is read. It simply
  restricts the reader from seeing any intermediate, uncommitted, 'dirty' read.
- REPEATABLE READ - If the transaction re-issues the read, it will find the same data even data changes in another
  transactions.
- SERIALIZABLE - range-locks are managed, so Phantom read can't occur. This level is like REPEATABLE READ, but InnoDB
  implicitly converts all plain SELECT
  statements to SELECT ... FOR SHARE if autocommit is disabled. More Deadlock may happen when you update rows
  with this isolation level.

![img.png](img/isolation_levels.png)

### Non-repeatable reads (read skew)

![img.png](img/non_repeatable_reads.png)

### Phantom reads

![img.png](img/phantom_reads.png)

### Lock in mysql

Non-locking read is default. For locking read use FOR SHARE, FOR UPDATE

#### SELECT ... FOR SHARE

Sets a shared mode lock on any rows that are read. Other sessions can read the rows, but cannot modify them until your
transaction commits. If any of these rows were changed by another transaction that has not yet committed, your query
waits until that transaction ends and then uses the latest values.

It's not same as transaction lock on update. You lock data not on update but on select.

#### SELECT ... FOR UPDATE

Other transactions are blocked from updating those rows, from doing SELECT ... FOR SHARE, or from reading the data in
certain transaction isolation levels (SERIALIZABLE).
When do locking read be sure to use fields with index preferably unique, to not lock all scanning rows in table.

### ETC...

- A transaction will timeout after 50 seconds  see innodb_lock_wait_timeout