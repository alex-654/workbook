- Service object - always immutable
- Value object - better be immutable
- Entities - mutable

### Entities

Entities mutable so they need identifier. Entities are core application objects.
Exm order, an invoice, a product, a customer, etc.

- Methods that change the entity’s state should have a void return type and
  their names should be in the imperative form (e.g., addLine(), finalize()).
- These methods have to protect the entity against ending up in an invalid state
  (e.g., addLine() checks that the invoice hasn’t been finalized already).
- The entity shouldn’t expose all its internals to test what’s going on inside.
  Instead, an entity should keep a change log and expose that, so other objects
  can find out what has changed about it, and why. (Do not implement getters methods just for test)
- Entity need to track they internal changes

### Value object

We shouldn’t change a value object at all.
If we want to transform it to some other value, we should just instantiate a new copy

```php
final class Quantity
{
    private int $quantity;
    private int $precision;
    
    private function __construct(int $quantity, int $precision)
    {
        $this->quantity = $quantity;
        $this->precision = $precision;
    }
    
    public static function fromInt(int $quantity,int $precision): Quantity 
    {
        return new Quantity($quantity, $precision);
    }
    
    public function add(Quantity $other): Quantity
    {
        Assertion::same($this->precision, $other->precision);
        return new Quantity($this->quantity + $other->quantity, $this->precision);
    }
}
    $originalQuantity = Quantity::fromInt(1500, 2);
    $newQuantity = $originalQuantity->add(Quantity::fromInt(500, 2));
```


- Most objects that are not entities should be implemented as immutable value objects
- A modifier on an immutable object should return a modified copy.
- It’ll always be safe to keep a reference to an immutable object
- Give modifier methods a name that’s domain-oriented rather than technical
- Calling a modifier method should always result in a valid object.
  Better reuse constructor instead of copy function (keep validation in one place)
- A modifier method should verify that the requested state change is valid

```php
final class SalesOrder
{
    public function markAsDelivered(Timestamp $deliveredAt): void
    {
        // You shouldn't be able to deliver the order if it has been cancelled.
    }
    
    public function cancel(Timestamp $cancelledAt): void
    {
        // You shouldn't be able to cancel an order if it has already been delivered.
    }
}
```

- Methods that change the state of an object should always be command methods (return void and imperative name)
- On mutable object modifier methods should have a name in the imperative form, they’re allowed to make a change
  to the object’s internal data structures, and they don’t return anything.
  Exp. moveLeft(): void
- On immutable object modifier method should have declarative names
  Exp: 
  - public function toTheLeft(int steps): Position
  - public withPassword(string plainTextPassword): User
- Use internally recorded events to verify changes on mutable objects
- Don’t implement fluent interfaces on mutable objects. It's violate CQS principle (return object instead of void)

### DTO

No strict rules for DTO. If language allow, mark public properties as read-only/write-once.