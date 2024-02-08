Transaction in Yii2 with savepoint

```php
        $transaction = User::getDb()->beginTransaction();
        $user        = User::findOrExcept(134203);
        $order       = Order::findOrExcept(1562966);
        try {
            $user->setName("PETRO");
            $user->saveOrExcept();

            $transaction->begin(); //create savepoint
            $order->setSum(16);
            $order->saveOrExcept();
            throw new ValidException("test");
        } catch (\Exception $e) {
            $transaction->rollBack(); //rollback to savepoint
            $transaction->commit(); // commit first part of code before savepoint
            throw $e;
        }
```

Yii for insert execute command by PDO, then execute sql function lastInsertId to get id for new entity.

For query

```php
        $orders = Order::find()
            ->joinWith('receivePoint')
            ->innerJoinWith(['delivery.userActiveKey'])
            ->byStatus(OrderStatusDictionary::ARRIVED)
            ->all();
```

In DB we get next 4 query (->joinWith() and ->innerJoinWith() by default with eager loading)

```sql
"SELECT * FROM `user_key` WHERE (`user_id` IN (16929, 110960, 114522, 119961, 120000, 120142, 120938, 121417, 125760, 129124, 139119, 139476, 140047, 143712)) AND (date_to is null or date_to > now()) ORDER BY `id` DESC"
"SELECT * FROM `user` WHERE `id` IN (16929, 129124, 114522, 139476, 119961, 120938, 140047, 125760, 139119, 110960, 120142, 121417, 143712, 120000)"
"SELECT * FROM `order_point` WHERE (`order_id` IN (1707455, 1707386, 1707336, 1707324, 1707295, 1707293, 1707285, 1707244, 1707211, 1707201, 1707181, 1707173, 1707165, 1707162, 1679375)) AND (`order_point`.`type`=1)"
SELECT `order`.*
FROM `order`
         LEFT JOIN `order_point` ON (`order`.`id` = `order_point`.`order_id`) AND (`order_point`.`type` = 1)
         INNER JOIN `user` ON `order`.`delivery_id` = `user`.`id`
         INNER JOIN `user_key` ON (`user`.`id` = `user_key`.`user_id`) AND (date_to is null or date_to > now())
WHERE `order`.`status` = 20
ORDER BY `id` DESC

```