### Конкурентный доступ и виды блокировок

В MySQL используется два типа блокировок: разделяемые (shared locks) и монолитные (exclusive locks)
Блокировки на чтение являются разделяемыми, или неблокирующими.
Это означает, что множество клиентов может читать запись в одно и то же время.
Блокировки на запись являются эксклюзивными, то есть они не дадут другим клиентам захватывать блокировки на
чтение/запись,
поскольку это единственный безопасный способ гарантировать наличие единственного клиента, записывающего в данный момент
времени.

Как получить дед лок

```
T1    | T2
El x  |    
      | El y
El y  |    
      | El x
```

В ДедЛок ключевое слово лок.
Есть две транзакции
1 наложила эксклюзивный лок на запись x и держит его
2 наложила эксклюзивный лок на запись y и держит его
1 транзакция хочет наложить эксклюзивный лок на запись y, но ждет когда он освободится 2 транзакцией
2 транзакция хочет наложить эксклюзивные лок на запись x, но ждем когда он освободится 1 транзакцией

И получается что они ждут др. др. Включится механизм БД по разрешению дедлоков. Одна из транзакций применится, другая
нет.

### Intention Locks

The main purpose of intention locks is to show that someone is locking a row, or going to lock a row in the table.
The intention locking protocol is as follows:

- Before a transaction can acquire a shared lock on a row in a table, it must first acquire an IS lock or stronger on
  the table.
- Before a transaction can acquire an exclusive lock on a row in a table, it must first acquire an IX lock on the table. 


Задание расставить транзакции
```php
public function buyItem($itemId, $userId)
{
     $item = "select * from item where id = {$itemId}";
     $user = "select * from user where id = {$userId}";
     
     if ($user->balance < 0 || $user->balance < $item->cost) {
        throw new Exception("error");
     }
     $updateUserBalance = "UPDATE user set balance = balance - {$item->cost} where id = {$itemId}";
     $updateCompanyBalance = "UPDATE company set balance = balance + {$item->cost} where id = 1}";
     $updateItemCount = "UPDATE item set count = count - 1 where id = {$itemId}";
}

// Один из вариантов решения открыть транзакцию в самом вверху
// поставить лок для юзера и товара в зависимости от политики бизнесса, чтобы их данные никто не поменял во время списания баланса.
// Данные для чтения в других транзакция (not lockin read) будут доступны
// Но на запись данные будут не доступны.
// Если где то будет еще стоять запись FOR UPDATE, то данные не будут прочитанны до момента сняти лока с текущей транзакции.
// Минусы если с фронта к примеру прийдет повторный запрос на списание, и у него будет достаточно денег, то эти запросы пройдут.
// И тут нужен кеш или мьютех или др.
public function buyItem(int $itemId, int $userId): void
{
     $transaction = $this->getDb()->beginTransaction(); //defalt level of isolation
     $item = "select * from item where id = {$itemId}"; // item можно не блокировать на изменения, если политка позволяет уйти по кол-ву товаров в минус.
     $user = "select * from user where id = {$userId} FOR UPDATE"; // При FOR SHARE можем получить deadlock если прийдет повторный запрос, а текущая транзакция не успеет выполниться
     
     $newBalance = $user->balance - $item->cost;
     if ($newBalance < 0) {
        throw new Exception("Low Balance for user - id");
     }
     
     try {
        $updateUserBalance = "UPDATE user set balance = {$newBalance} where id = {$itemId}";
        $updateCompanyBalance = "UPDATE company set balance = balance + {$item->cost} where id = 1}";
        $updateItemCount = "UPDATE item set count = count - 1 where id = {$itemId}";
     } catch(Throwable $throwable) {
        $transaction->rollBack();
        throw new Exception("Tranastion aborted. some data");
     }
     $transaction->commit();
}
```

```php
// Можно повысить уровень транзакции до SERIALIZABLE
// Транзакция открывается вверху.
// Из минусов тут нельзя будет выбрать что можно залочить, а что нет.
// Можно получить деадлок, если одна транзакция не успеет завершить update часть
// Но проблемы потерянных данных можно будет избежать
public function buyItem($itemId, $userId)
{
    $transaction = $this->getDb()->beginTransaction(Transaction::SERIALIZABLE);  
     // ... code
     
    $transaction->commit();
}
```

```php
// Еще есть вариант сделать через lock/mutex в бд (SELECT GET_LOCK)
// Из плюсов юзеру можно отправить сообщение о том, что его предудущая транзакция еще в работе
public function buyItem($itemId, $userId)
{
     $mutex    = Yii::$app->mutex;
     $lockName = "buy_item_user_id_{$userId}";
     if (!$mutex->acquire($lockName)) {
        throw new Exception("User has lock.");
     }
     // ... code
     
     $mutex->release($lockName);
}
```
