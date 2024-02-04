## Test types

### Matias Noback divide test in his book (advanced web application architecture)

- unit (isolated, test small part like an entity or value object)
- integration (test for adapters incoming and outgoing)
    - Contract tests for outgoing port adapters (DB, external DataProviders)
    - Driving tests for incoming port adapters (Controllers, API, CLI)
- use-case (isolated like a unit)
- end-to-end (test from start to end)

### Cоdeception test framework divide test on

- Acceptance (automated test with browser emulation like PhpBrowser)
- Functional (the test runner exercises the application's front controller programmatically, instead of through a web
  server)
- Unit

### stackoverflow answer 1

- Unit Tests are written from a programmer perspective.  
  They are made to ensure that a particular method (or a unit) of a class performs a set of specific tasks.
- Functional Tests are written from the user's perspective.  
  They ensure that the system is functioning as users are expecting it to.

### stackoverflow answer 2 (V-model)

https://en.wikipedia.org/wiki/V-model_(software_development)

**Test level** is easy to explain using V-model

![](img/v-model.png)

**test types**

Test types emphasize your quality aspects, also known as technical or non-functional aspects.
Test types can be executed at any test level.
I like to use as test types the quality characteristics mentioned in ISO/IEC 25010:2011.

- functional testing
- reliability testing
- performance testing
- operability testing
- security testing
- compatibility testing
- maintainability testing
- transferability testing

### stackoverflow answer 3

One of test groups.
There are two big groups: Functional and Non-Functional Testing.

![](img/test_type.png)

## Given/When/Then syntax:

- Given the user with ID 1 has username "alice"
- When getting the username of the user with ID 1
- Then the username is "alice"

1. Givens should be in the past tense
2. Whens should be in the present tense
3. Thens should be in the future tense (often using "should" or "will")

```php
// Given the system has been configured to reroute all SMS messages to the email address developers@org.nl
$config = Mockery::mock(Config::class);
$config->shouldReceive('get')
    ->with('reroute_sms_to_email')
    ->andReturn('developers@org.nl');

// When the system sends an SMS
$fallbackMailer = Mockery::spy(Mailer::class);
$smsSender = new SmsSender($config, $fallbackMailer);
$smsSender->send('0612345678', 'The message');

// Then the SMS message will be sent as an email to developers@org.nl instead
$fallbackMailer->shouldHaveReceived('send')
    ->with(function (Mail $mail) {
        self::assertEquals('The message', $mail->plainTextBody());
        self::assertEquals('SMS for 0612345678', $mail->subject());

        return true;
    });

```

## M. Noback on tests

- We should consider test methods to be documentation or more specific: specification.
- "Don't Mock What you Don't Own" (Wrap the third party library, create interface and default realization with fake/test
  realization

### Drawbacks of functional tests

- Faking a Request object
- Since you have access to the application's service container you may be tempted to open the black box and replace all
  kinds of services, or otherwise influence them. I recommend doing no such thing.
- You are likely to end up testing all kinds of domain-related behaviors through thick layers. This obscures the view on
  the real issues and makes it hard to figure out what's wrong, if anything pops up.
- "I see in database". This too is like opening the box and looking inside.  
  I'd like to follow this reasoning instead: if, as a user of the system, I expect a request to produce some kind of
  effect, then I should be able to notice this effect as a user. And as a user I can't look directly inside the
  database. There must be some other aspect of the system that was changed when I made that request. E.g. after
  registration I can now log in, or the product I added to my basket actually shows up on the "my shopping basket"
  page.  
  But always look for ways to prevent this, and let your application remain the black box that you poke at with HTTP
  requests only, making only assertions about the response you receive.