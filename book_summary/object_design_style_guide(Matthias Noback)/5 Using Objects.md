### Template for methods

```php
[scope] function methodName(type name, ...): void|[return-type]
{
    [preconditions checks]
    [failure scenarios]
    [happy path]
    [postcondition checks]
    [return void|specific-return-type]
}
```

### Precondition checks
- Throws InvalidArgumentException
- Superficial, because they inspect the values for obvious issues
```php
if (/* some precondition wasn't met */) {
throw new InvalidArgumentException(/* ... */);
}
Assertion.inArray(value, ['allowed', 'values']);
Assertion.allIsInstanceOf(value, EventListener.className);
```
Introduce new types to get rid of precondition checks (replace primitive with object).

### Failure scenarios
For example, even though an email address looks valid, sending an email to it might still fail.  
Or even though the client provides a positive integer, it might not match a record ID in the database.
Type of the exception should indicate that an error condition occurred that could only be detected at runtime. 
Exp. - RuntimeException.

### Happy path
Where method is just performing its task

### Postcondition checks
- Analyze the return value before actually returning it, 
  or you could analyze the state of the object just before jumping out of it
- More helpful for legacy code

### Return value
- Only query methods should do that
- Return early. As soon as you know what you will return, 
  return it right away instead of keeping the value around, skipping a few more if clauses, and then returning it

## Rules for exceptions
- Use custom exception classes only if needed
- Naming invalid argument or logic exception classes Exm. InvalidEmailAddress, InvalidStateTransition
- Naming runtime exception classes Exm. CouldNotFindProduct, CouldNotStoreFile, or CouldNotConnect
- Use named constructors to indicate reasons for failure
- Add detailed messages
```php
final class CouldNotFindProduct extends RuntimeException
{}
// At the call site:
    throw new CouldNotFindProduct("Could not find a product with ID '{$productId}'");
// After:
final class CouldNotFindProduct extends RuntimeException
{
    public static function withId(ProductId $productId): CouldNotFindProduct
    {
        return new CouldNotFindProduct("Could not find a product with ID '{$productId}'");
    }
}
// At the call site:
throw CouldNotFindProduct::withId($productId);
```