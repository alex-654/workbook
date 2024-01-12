## 5 Service locators

### 5.1 From service location to explicit dependencies

It’s not a very nice thing to introduce a dependency on framework code inside core code.  
If we inject actual services instead of the service container the application service will only be coupled to our own
core interfaces and classes.

### 5.2 Depending on global state

In core code must not be

- HTTP request/response
- session
- IP address
- config()

Normally, the framework will take care of all of this by preparing the service container and loading the
configuration before the application service is used.

- If we see this dependencies code isn’t as portable as we would like it to be.
  Your code will be more future-proof if you don’t
  rely on framework-specific helpers or “syntactic sugar”, which are subject to fashion.
- Your code will also be easier to test, and will remain easier to test,
  even when you have migrated to a different framework.
- In fact, if you depend on framework-specific classes and functions, it will be very hard to
  migrate to another framework because you will have to make changes
  everywhere.

### 5.3 Injecting dependencies

```php
final class SendIpConfirmationEmail
{
    private Mailer $mailer;
    
    public function __construct(Mailer $mailer)
    {
        $this−>mailer = $mailer;
    }
    
    public function send(): void
    {
        // ...
        $this−>mailer−>send($message);
    }
}
```

I like to read a service’s constructor as a list of things that the service needs.
Compare this to having to read the entire method just to find out which dependencies will be fetched at runtime.

### 5.4 Injecting configuration values

### 5.5 Using method arguments for job-specific data

In service in constructor we add service dependencies and configuration values. Exm. Translator service, admin email.
In method, we pass contextual data (key word current). Exm. current user, current ip.

### 5.6 Clients of reusable services

So if code is platform\environment independent we now can add more client to our service.

### 5.7 Testing

In theory, you should be able to write a unit test for every class. In practice, you may not want to do this, since
different areas of a code base require different types of tests.  
A unit test is a test that doesn’t use any IO (it doesn’t use the file system, a database, an external service, etc.)
and usually but not necessarily covers a smaller unit of code, like a single class.

With explicit dependencies throw constructor it is easy to test.
If you miss something it is clear what you miss.

### 5.8 Effective testing

But it always pays to find a way to separate the logic from the infrastructural concerns.  
If you can do this, you will end up with two islands of code:

- for the one you can write unit tests,
- for the other integration tests.
  The bigger the first island is, the better.

### 5.9 The Composition root is near the entry point

A Composition Root is a (preferably) unique location in an application
where modules are composed together.

The entry point is the user code that the framework calls first.

Seemann suggests that at or near the entry point of the application, it seems
as if we’re using the container as a service locator, but in fact, we use it as a
composition root, which is a different role.

### 5.10 Summary
