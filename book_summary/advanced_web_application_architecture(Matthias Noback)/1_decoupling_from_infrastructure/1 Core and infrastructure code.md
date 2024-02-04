### Core code

In a web application, infrastructure supports the communication between your application and the outside world.
The core is the center of your application, the infrastructure is around it.

#### Rule no 1: No dependencies on external systems.

- Core code doesn’t directly depend on external systems, nor does it depend on code written for interacting with
  external system.
- External systems - database, some remote web service, the system’s clock, the file system, and so on.
- Core code should be able to run without these external dependencies.
- When code follows the first rule, it means you can run it in complete isolation.
- Isolation is great for testability.
- Abstraction is the go-to solution to get rid of dependencies on external systems

#### Rule no 2: No special context needed.

Core code doesn’t need a specific environment or specific context to run in.
Exp. of dependencies on environment

- only works at the command line php_sapi_name() !== ’cli’
- uses global state $host = $_SERVER[’HTTP_HOST’];
- uses static service locator $translator = Zend_Registry::get(’Zend_Translator’);
- controllers for web applications only

```php
// This is a proper abstraction for an object that talks to the database
interface MemberRepository
{
    public function save(Member $member): void;
}

final class MemberService
{
    private MemberRepository $memberRepository;
    
    public function requestAccess(string $emailAddress, string $purchaseId): void
    {
        $member = Member::requestAccess(
            EmailAddress::fromString($emailAddress),
            PurchaseId::fromString($purchaseId)
        );
        $this−>memberRepository−>save($member);
    }
}
```

“Is all code in my vendor directory infrastructure code?”

- web framework, ORM yes
- but can be same code that can be core and even not written specifically for you app.