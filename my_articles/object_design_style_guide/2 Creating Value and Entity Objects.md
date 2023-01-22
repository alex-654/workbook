## Creating value/entity objects

Value object exp. Coordinate, DateRange, Money, Currency, NumberInterval, EmailAddress
Entity/model (User, Orders)

### Rules

- Require the minimum amount of data needed to behave consistently.  
  Most properties should be required in constructor. Fewer setters or assignment.
- Private properties
- Validate the domain invariants (exm. longitude is float between -180 and 180).  
  Throw exception on wrong params.
- Use one or more named constructors instead of bool params in constructor
- Don’t use custom exception classes for invalid argument exceptions. InvalidArgumentException should be enough.
  Invalid argument means that object is used in wrong way and it better fail hard.
- Test for specific invalid argument exceptions by analyzing the exception’s message.
  By looking on Exception message should be clear what argument/property is wrong

```php
final class Coordinates
{
    public function __construct(float latitude, float longitude)
    {
        if (latitude > 90 || latitude < -90) {
            throw new InvalidArgumentException('Latitude should be between -90 and 90');
        }
        this.latitude = latitude;
        if (longitude > 180 || longitude < -180) {
            throw new InvalidArgumentException('Longitude should be between -180 and 180');
        }
        this.longitude = longitude;
    }
}
```

- Extract new objects to prevent domain invariants from being verified in multiple places.  
  EmailAddress object instead of string.
  ```php
  final class User
  {
      private EmailAddress emailAddress;
      public function __construct(EmailAddress emailAddress)
      {
          this.emailAddress = emailAddress;
      }
      // ...
      public function changeEmailAddress(EmailAddress emailAddress): void
      {
          this.emailAddress = emailAddress;
      }
  }
  ```
  As soon as you notice that a method accepts a primitive-type value (string, int, etc.), you should consider
  introducing a class for it.
  The guiding question for deciding whether or not to do this -
  “Would any string, int, etc., be acceptable here?”

- Extract new objects to represent composite values
  ```php
  final class Money
  {
    public function __construct(Amount amount, Currency currency)
    {
    // ...
    }
  }
  ```

- Use assertions to validate constructor arguments
- Don’t collect exceptions. Throw them asap.
- Don’t inject dependencies; optionally pass them as method arguments. Opposite to Service Object.
- Better to use named constructor instead of __construct(). Exp. fromString(), fromInt() etc.
  Make standard constructor private to force use named constructor
  They can be used to introduce domain-specific synonyms for creating an object.
- Don’t use property fillers. Exp fromArray(). With this method Object lost full control on its data.
- Don’t put anything more into an object than it needs. Use test-driven way this means 
  that you first have to know how an object is going to be used