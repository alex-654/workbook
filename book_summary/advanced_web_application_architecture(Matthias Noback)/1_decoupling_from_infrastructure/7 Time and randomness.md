### 7.1 Passing current time and random data as method arguments

How can we turn this test back into a unit test? By making Order a “pure”
object again. We have to make sure that its behavior doesn’t depend on
anything else than its constructor arguments, its own implementation, and
the method arguments provided to it.

- We should make the object no longer use the actual current time, or a truly random number.
- Instead, we should pass the current time or random data to it as method arguments.

### 7.2 Introducing factories

Even though Uuid and DateTimeImmutable look like value objects, they really aren’t.

- When an object talks to external systems this object should be a service.
- And when we create a service class for something that talks to the outside world, we should always provide an
  interface for it.

For the creation of a DateTimeImmutable instance we might consider
introducing a TimeFactory, but this is commonly known as a Clock anyway.

```php
interface Clock
{
    public function currentTime(): DateTimeImmutable;
}

final class ClockUsingSystemClock implements Clock
{
    public function currentTime(): DateTimeImmutable
    {
        return new DateTimeImmutable(’now’);
    }
}
```

- Whenever we need a DateTimeImmutable instance we use the Clock.
- When we need a Uuid, we use the UuidFactory.

### 7.3 Introducing value objects

- Rule of thumb is that the classes you use as dependencies (constructor arguments, method arguments and return
  types) in your domain model should also be designed by you.
- It’s a good thing to make every aspect of your domain model’s design explicit, and suitable for your particular use
  case.

In the case of our use of Uuid and DateTimeImmutable, the solution will be to define our own value objects.
This solves two issues at the same time:

- We’ll have complete control over the API of our domain objects.
- We won’t accidentally use IO in our domain objects, nor in the unit tests we create for them.

It’s perfectly fine to rely on other people’s classes inside your value object, as long as they don’t reintroduce
some of the problems we’ve been trying to evade:

- Value objects shouldn’t contain infrastructure code. In other words:
- Value objects should have no service responsibilities.
- Value objects should offer no behavior that hasn’t been explicitly enabled and designed for your use case.

### 7.4 Improving the factories

### 7.5 Manipulating the current time

In a test, you can easily configure the “current” time.

```php
final class CreateOrderTest extends TestCase
{
    public function it_creates_an_order(): void
    {
        $clock = new class implements Clock {
            public function currentTime(): DateTimeImmutable
            {
                return new DateTimeImmutable(’2019−07−09’);
            }
        };
        $service = new EbookOrderService(
            new CalendarUsesClock($clock),
            // ...
            );
        $service−>create(/∗ ... ∗/);
    }
}
```

Listing 7.20: Using Clock and OrderRepository.

```php
final class EbookOrderService
{
    private Clock $clock;
    private OrderRepository $orderRepository;
    // ...
    public function __construct(
        Clock $clock,
        OrderRepository $orderRepository,
        // ...
    ) {
        $this−>clock = $clock;
        $this−>orderRepository = $orderRepository;
        // ...
    }
    
    public function create(/∗ ... ∗/): OrderId
    {
        $order = Order::create(
            $this−>orderRepository−>nextIdentity(),
            Date::fromCurrentTime(($this−>clock−>currentTime()),
            // ...
        );
    // ...
}
```

Listing 7.15: Instantiating Date based on an already existing DateTimeImmutable instance.

```php
final class Date
{
    private const DATE_FORMAT = ’Y−m−d’;
    private string $date;
    
    private function __construct(string $date)
    {
        // ...
    }
    
    public static function fromCurrentTime(DateTimeImmutable $dateTimeImmutable): self {
        return new self(
            $dateTimeImmutable−>format(self::DATE_FORMAT)
        );
    }
}
```

“jumpy” code - where we go from application service to abstraction, to concrete class, to abstraction, to concrete
class.

### 7.6 Integration tests again

Tests for infrastructure code are called integration tests.