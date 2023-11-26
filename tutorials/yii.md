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