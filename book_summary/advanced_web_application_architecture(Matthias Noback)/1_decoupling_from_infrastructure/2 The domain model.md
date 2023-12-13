A stateful object that guarantees its own consistency, and is going to be persisted somehow, is
often called an Entity

### 2.5.1 Using an ORM

it’s okay to use an ORM if you can stick to the following rules:

1. Only use simple mapping configuration; no table inheritance, “embeddables”, custom types
2. Stick to one-to-many associations.
3. Reference entities by their ID.
4. Don’t jump from entity to entity using association fields.

Instead of using ORM (Doctrine) we can map entity properties to db table inside object

```php
final class Order
{
// ...
}
public function mappedData(): array
{
    return [
    ’email’ => $this−>emailAddress,
    ’quantity’ => $this−>quantityOrdered,
    // ...
    ];
}

final class SqlOrderRepository implements OrderRepository
{
// ...
public function save(Order $order): int
{
    // ...
    $data = $order−>mappedData();
    // $data is an array of columns => values
}
```

mappedData method contains technical implementation details (like table and column names and column types) but no
dependencies on external code. Can be tested without DB.

### 2.6 Generating the identifier earlier