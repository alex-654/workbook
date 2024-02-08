## 8 Validation

- if I would migrate from a web application to a CLI application, would input data still have to be validated?
- if I change the technical solution I use for saving my data (e.g. when I switch to a document database), would I still
  be able to protect the consistency of this data? Imagine not being able to rely
  on foreign key constraints or unique indexes;

### 8.1 Protecting entity state

Options:

- separate validate service. That must be explicitly called each time before save.
- validation on entity create with constructor
- validation on entity save

We can make the entity self-validating. Whenever a client tries to create a new instance of the entity, it
can enforce a minimum of required data to be provided as constructor
arguments. Whenever a client wants to provide some more data or modify
existing data, the entity can again validate the provided arguments before
assigning any new values to its properties. You could say that this isn’t
validation, it’s more like protection. We don’t assign a value to a property,
then validate it; we protect the object from ending up in an incorrect state.

- The exception message and the accompanying stack trace should only be visible to developers.

### 8.2 Using value objects to validate separate values

### 8.3 Form validation

For form validation we can’t rely on entity-level exceptions.  
Because exception:

- only for programmer,
- can expose some confidential information,
- not user-friendly.
- can be shown one at time.

Form is a very web specific thing so its infrastructure code.

- we should never remove the validation from the value object

Validating an ID using the repository.

```php
public function createOrderAction(Request $request): Response
{
    $formErrors = [];
    try {
        new EmailAddress($request−>get(’email’));
    } catch (InvalidArgumentException $exception) {
        $formErrors[’email’][] = ’create_order.invalid_email_address’;
    }
    try {
        $this−>ebookRepository−>getById(EbookId::fromString($request−>get(’ebook_id’)));
    } catch (CouldNotFindEbook $exception) {
        $formErrors[’ebook_id’][] = ’create_order.could_not_find_ebook’;
    }
    // ...
}
```

If I’d find this code in a real project, I would have two comments:

1. All of this validation code clutters the view on what this controller
   really does. Let’s move it out of the controller.
2. We’ve removed the duplication by reusing validation logic, but we
   still duplicate the effort. Everything gets checked twice. Are you sure
   we can’t skip one of these checks?

So the best way is to create a validation on frontend and keep validation in application level throw value object.

Since throwing an entity-level exception is a just-in-case protection
mechanism, you should keep track of them. If you find these exceptions in
your server logs, it usually means that:

- Somebody is abusing your application, or
- You should improve the user interface so users can’t make the same mistake again.

### 8.4 Using exceptions to talk to users

“Shouldn’t exceptions only be used in exceptional situations?”  
If it turns out that an exception is no
longer exceptional, that is, it gets thrown a lot, this is a strong sign that we
need to improve the user experience somehow.

Preventing bad things from happening to our domain objects is a great reason for throwing exceptions.

### 8.5 When validation is not the answer

->numberOfAvailableBooks() is not pure function.

good rule of thumb would be:

- When validating input data, only use pure functions.
- It means that validation itself, if seen as a function, becomes a pure function itself.
- If you validate the data now and it’s valid, it should still be valid when you validate it again.
- Likewise, if it’s invalid, the next time you validate the data, it should also be invalid.

### 8.6 Creating and validating command objects

```php
final class OrderEbook
{
    use Mapping;
    
    private string $emailAddress;
    private int $ebookId;
    private int $quantity;
    private ?string $buyerName;
    
    // ...
    
    public static function fromRequestData(array $data): self
    {
        return new self(
            self::getString($data, ’email’),
            self::getInt($data, ’ebook_id’),
            self::getInt($data, ’quantity’),
            self::getNonEmptyStringOrNull($data, ’buyer_name’)
        );
    }
}
```

### 8.7 Summary
