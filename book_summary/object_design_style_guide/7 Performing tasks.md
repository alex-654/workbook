### Use command methods with a name in the imperative form

### Limit the scope of a command method, and use events to perform secondary tasks

Don’t do too much in one method. Guiding questions

- Should or does the method name have “and” in it, to indicate what else it does besides its main job?
- Do all the lines of code contribute to the main job?
- Could a part of the work that the method does be performed in a background process?

When two tasks belong together, and we don’t want to mix them in one method use events.  
Using events like this has several advantages:

- You can add even more effects without modifying the original method.
- The original object will be more decoupled because it doesn’t get dependencies
  injected that are only needed for effects.
- You can handle the effects in a background process if you want.

A possible disadvantage of using events is that the primary action and its secondary
effects may be implemented in remote parts of the code base. Make sure that events are always explicitly dispatched

### Make services immutable from the outside as well as on the inside

Even if you don’t offer clients a way to manipulate a service’s dependencies or configuration
Command methods may still change a service’s state in such a way that behavior will be different for subsequent calls
Would it be possible to reinstantiate the service for every method call, and would it still show the same behavior?

### When something goes wrong, throw an exception

### Use queries to collect information and commands to take the next steps
- if chain of methods starts with a query it shouldn't have a call to a command methods inside. 
- Inside a command method, you may call query methods to retrieve more information.

### Define abstractions for commands that cross system boundaries
If a command method has code that reaches out across the application’s own boundaries
(that is, if it uses a remote service, the filesystem, a system device, etc.), you 
should introduce an abstraction for it

### Only verify calls to command methods with a mock
Mock and Spy for commands, Stub, Dummy and Fake for query methods  