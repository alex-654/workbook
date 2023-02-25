Method should always be either a command or a query method Command/query separation principle (CQS)  
For query name should be noun, for command verb

### Use query methods for information retrieval

Query methods it is safe methods that can be called any time

Is a modifier method a command or a query method?
A modifier method doesn’t really return the information you’re after. In fact, it returns
a copy of the whole object, and once you have that copy, you can ask it questions.
So modifiers don’t seem to be query methods, but they aren’t traditional command
methods either. A command method on an immutable object would imply that it
changes the object’s state after all, which isn’t the case. It produces a new object,
which isn’t far from just answering a query.
Although it’s stretching the concept a bit, the incremented() method in listing 6.3
could answer the query “give me the current count, but incremented by 1.”

### Query methods should have single-type return values

May help with it

- throw Exception
- return an object that can represent the null case
- return a result of the same type, but representing the empty case ([], 0, '')
- add two methods with null and with Exception

```php
public function getOneByType(PageType $type): Page
{
    $page = $this->findOneByType($type);
    if (!$page instanceof Page) {
        throw PageNotFound::withType($type);
    }
    return $page;
}
```

### Avoid query methods that expose internal state

- The things a client does with the information that an object provides can often be done by the object itself
- With small rewrites like this, you can make an object absorb more and more logic,
  thereby keeping the knowledge about the concept it represents inside it, instead of
  scattering little bits of this knowledge all over the code base.
- An object can be seen as having boundaries. Instead of allowing clients to cross
  those boundaries to retrieve information from the object, define explicitly which data and
  which behaviors should be available to clients.

### Define specific methods and return types for the queries you want to make

- Ask question and answer them
- Don't do too general queries

### Define an abstraction for queries that cross system boundaries (connect to a remote service, use file system or time)

Abstraction:

- Using a service interface instead of a service class
- Leaving out the implementation details

Abstraction will make it possible

- To run your code in a test scenario, without making the actual network or filesystem calls
- Swap out implementations without having to modify the client code

This rule similar to dependency inversion principle.
Pick a more abstract name that stands for what we’re doing (GeoCodeProvider, ExchangeRateProvider)

### Use stubs for test doubles with query methods

- A fake is one kind of test double, which can be characterized as showing “somewhat complicated” behavior,
  just like the real implementation that will be used in production.
- A stub is a test double that just returns hardcoded values
- On query method don't test number of calls or order
- On command method do test particular order, how many calls have been made.
- Don’t use mocking tools for creating fakes and stubs (code is getting hard to read and maintain)
- Unit test - test the behavior of an object in memory.
- Acceptance testing can be performed by a non-technical person.  
  That person can be your tester, manager or even client.
  You can reproduce an acceptance tester’s actions in scenarios and run them automatically.
- Functional tests are almost the same to Acceptance, with one major difference:  
  Functional tests don’t require a web server. (For API)
- Integration test (exp. query to other API) - tests the integration of an object
  with the thing in the world outside that it relies on.

### Query methods should use other query methods, not command methods

- Calling a command method somewhere in the chain will violate that rule
- Controller violates the command/query separation principle, but there’s no way around that