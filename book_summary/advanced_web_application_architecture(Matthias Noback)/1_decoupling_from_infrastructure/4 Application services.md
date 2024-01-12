### 4.1 Considering other infrastructures

So we have code in controller.
If we want to realize the same functionality in CLI or API?

- copy code
- call controller from cli

### 4.2 Designing a use case to be reusable

To make a use case reusable we should find a common ground that allows
different types of clients to invoke the same use case. If you start with an
existing controller like the one we are working on right now, you should
look for the things that tie the code to specific infrastructure and then
decouple it from these elements. For instance, the code currently relies on a
Request object, which makes it only useful in a web context, and the same
goes for passing data to the next request by storing something in a Session
object. An API controller should not keep state between requests, and a CLI
client wouldn’t even know about sessions. So to make the use case code
reusable we should remove its dependencies on Request and Session.

### 4.3 Extracting an application service

Move all the infrastructure-independent code to its own class

### 4.4 Introducing a parameter object

The number of method parameters of a service like, may get quickly out of hand. If not leave as primitive data types.

Refactoring - Introduce Parameter Object.
It makes sense to name this object after its intention. You want to “create an order” with it, so let’s call it
CreateOrder.
Such an object is often called a Command object (realized as DTO). It’s called a command
because the object represents a user intention: something the user wants the application to do.

### 4.5 Dealing with multiple steps

For now in controller we have
1 one service for order create
2 code that send a conformation email.

```php
public function orderEbookAction(Request $request): Response
{
    // ...
    $orderId = $this−>container−>get(’ebook_order_service’)−>create($createOrder);
    $this−>container−>get(’session’)−>set(’currentOrderId’, $orderId−>asString());
    $message = (new Swift_Message(’Your Order’))
    −>setFrom($this−>container−>getParameter(’system_email_address’))
    −>setTo($request−>request−>get(’email_address’))
    −>setBody(
    $this−>container−>get(’twig’)−>render(’email/order_confirmation.html.twig’));
    $this−>container−>get(’mailer’)−>send($message);
    
    return new Response(/∗ ... ∗/);
}
```

Options

1. We can leave code with email in controller as it is. But if order create will be called not form controller. Email
   will be missed.
2. Next option move code with conformation email to ebook_order_service

- Then we can pass code as it is
- create interface SendOrderConfirmationEmail and our implementation
- we can send Email throw Event Dispatcher