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

### 2.7 Using a value object for the identifier

### 2.8 Active Record versus Data Mapper

Downside of AR

- By inheriting a lot of infrastructure code, we loose the isolation we need for proper unit
  testing an Order.
- Active Record frameworks usually require a lot of custom code inside your entities to
  make everything work well. This code is specific to the framework, making your domain
  model directly coupled to, and only functional in the presence of that framework.
- Clients of Order can do many more things with the object than they most likely should be
  allowed to do.

Downside of DM

- you have several extra elements: the repository interface and at least one repository implementation.
- we had to make a decision: should we do the mapping to database columns inside or outside the entity?

Advice is to mitigate some of the downsides of AR

- Design your AR entities like real entities. Take a look at Section 11.2.
- Don’t use the same AR entity for changing state and retrieving state. Separate your model
  into a write and a read model.
- Don’t use your AR entity to navigate from one AR entity to other AR entities. If you want
  to make a change to a different AR entity, fetch it by its ID from the corresponding
  repository.
- Ignore the fact that an AR entity provides typical service facilities like saving and deleting.
  Use a repository and double dispatch to perform these tasks.

### 2.9 Summary

Now that we have an entity class, a repository class, and a repository interface, we can tick all
the boxes and mark this code as core code:

1. The Order entity and OrderRepository interface don’t reveal anything about the client
   that’s going to call or use it.
2. The OrderRepository interface is itself an abstraction, using which we can later run
   other core code without actual databases, or other external dependencies.
3. The Order entity can indeed be used without an actual web server, database, etc. It’s a
   plain old PHP class that you can run without any special setup.