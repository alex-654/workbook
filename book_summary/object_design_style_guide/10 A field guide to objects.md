### Controllers

Front controller this is where all the requests come in. If you use PHP, this could be your index.php file.
For command-line (CLI) applications, the “front controller” would be the executable you’d call,
such as bin/console, artisan
Because controllers talk about the particular delivery mechanism that initiated a
call to them (the web, the terminal), controllers should be considered infrastructure
code. They facilitate the connection between the client, who lives in the world outside,
and the core of the application.

### Application services

An object is an application service if

- it performs a single task,
- it contains no infrastructure code; that is, it doesn’t deal with the web
  request itself, or SQL queries, or the filesystem, etc., and
- it describes a single use case that the application should have. It will often
  correspond one-to-one with a feature request from a stakeholder.
  For example, it should be possible to add a product to the catalog, to cancel an order,
  to send a delivery note to a customer, etc.

### Write model repositories

An object is a write model repository if . . .

- it offers methods for retrieving an object from storage and for saving it, and
- its interface hides the underlying technology that’s been used.

### Entities

The objects that are being persisted will be the ones the user cares about the ones
that should be remembered even when the application has to be restarted
They contain relevant data and offer useful behavior related to this data
An object is an entity if . . .

- it has a unique identifier,
- it has a life cycle,
- it will be persisted by a write model repository and can later be retrieved from it,
- it uses named constructors and command methods to provide the user with
  ways to instantiate it and manipulate its state, and
- it produces domain events when it gets instantiated or modified.

### Value objects

In the context of the journey from controller to application service to repository, it should be
noted that it’s often the application service that instantiates value objects and then
passes them as arguments to the constructor or a modifier method of an entity.
Therefore, they end up being used or stored inside the entity.

An object is a value object

- if it’s immutable,
- if it wraps primitive-type data,
- if it adds meaning by using domain-specific terms (e.g., this isn’t just an int,
  it’s a Year),
- if it imposes limitations by means of validation (e.g., this isn’t just any string,
  it’s a string with an '@' in it), and
- it acts as an attractor of useful behavior related to the concept (e.g., Position.toTheLeft(int steps)).

### Event listeners

An object is an event listener . . .

- if it’s an immutable service, with its dependencies injected, and
- if it has at least one method which accepts a single argument that is a domain event.

They can be used to notify other services about things that have happened inside the write model.
These other services can then perform secondary actions, after the primary work has been done.
Application service could fetch the recorded events from the entity it has modified,
and hand them over to the event dispatcher.
Internally, the dispatcher will forward all events to services called “listeners” or “subscribers,”
which have been registered for particular types of events.

```php
// A convenient naming standard for event listeners is the name of the thing you’re going to do (e.g., “notify group members”).
final class NotifyGroupMembers 
{
    // The methods will then point out the reasons for doing so (e.g., “when meetup rescheduled”).
    public function whenMeetupRescheduled(MeetupRescheduled $event): void 
    {
        /*
        * Send an email to group members using the information from
        * the event object.
        */
    }
}
```

### Read models and read model repositories

An object is a read model repository . . .

- if it has query methods that conform to a specific use case and will return
  read models, which are also specific for that use case.

An object is a read model . . .

- if it has only query methods, i.e., it’s a query object (and is therefore immutable),
- if it’s designed specifically for a certain use case, and
- if all the data needed (and no more) becomes available the moment you retrieve the object

### Abstractions, concretions, layers, and dependencies
The abstraction describes what you’re looking for, and the implementation describes how you can get it
Every repository need an interface.

- Controllers are concrete. They are often coupled to a particular framework and are
  specific for the delivery mechanism. They don’t need an interface.
  The only time you’d want to offer an alternative implementation is when you
  switch frameworks. In that case, you’d want to rewrite these controllers instead
  of creating a second implementation for them.
- Application services are concrete. They represent a very specific use case of your
  application. If the story of a use case changes, the application service itself
  changes, so they don’t have an interface.
- Entities and value objects are concrete. They are the specific result of the developer’s
  understanding of the domain. These types of objects evolve over time. We don’t
  provide an interface for them. The same goes for read model objects. We
  define and use them as they are, never through an interface.
- Repositories (for write and read models) consist of an abstraction and at least one concrete
  implementation. Repositories are services that will reach out and connect to
  something outside of the application, like a database, the filesystem, or some
  remote service. That’s why they need an abstraction that represents what the
  service will do and what it will return. The implementation will then provide all
  the low-level details about how it should do that. The same goes for other service objects
  that will reach out to some service outside the application. These
  services will also need an interface and a concrete implementation.

The services for which we have abstractions, according to the preceding list, should be
injected as abstract dependencies. If we do this, we can form three useful groups, or layers of objects:
1 The infrastructure layer:
– Controllers
– Write and read model repository implementations
2 The application layer:
– Application services
– Command objects
– Read models
– Read model repository interfaces
– Event listeners
3 The domain layer:
– Entities
– Value objects
– Write model repository interfaces