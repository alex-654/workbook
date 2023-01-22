## Creating service objects

### Service objects.

Common names - controller, renderer, calculator, logger.
Could I run this service in a batch, without reinstantiated?
If memory was not wiped after every web request, could this service be used for subsequent requests.
Build a Service object graph.
Controllers is the entry point that calls all other service. Services should be put inside DI container.
One service can be used by different controllers.

Service objects
- Will be created once, and then be used any number of times
- Nothing will be changed inside them. No Setters, No optional parameters.  
  If you don't have all parameters you can pass a dummy data (null object pattern)
- No accesses to service.locator (Turn static dependencies into object dependencies)
- Task-relevant data should be passed as method arguments instead of constructor arguments
  In fact, the word “current” is a useful signal that this information is contextual information
  that needs to be passed as method arguments: “the current time,” “the currently logged in user ID,” “the current web
  request,” etc.
- The only thing you should do in a service constructor is validate the provided
  arguments, and then assign them to the object’s properties. Throw an exception when an argument is invalid
- If in constructor manipulation needed, consider create Factory
- If inside constructor order of assignment matter you should think about better design
- Make system calls explicit (functions that reach out to the world outside)
  Exp do not call DateTime file_get_content in service. Pass them as construct arg or function arg.
  Or better create an interface for DateTime (Clock for code, FixedClock for test).
- Turn complicated functions into object dependencies 
  (exm json_decode with flags which throw Exception instead of default Error)

Profit  
- Easy replace dependencies in test or in DI container
- Grasp all dependencies in one place
- More efficiency in code
- Less if