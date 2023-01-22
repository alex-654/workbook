### Introduce constructor arguments to make behavior configurable

Instead of this

```php
final class FileLogger
{
    public function log(message): void
    {
        file_put_contents('/var/log/app.log', message, FILE_APPEND);
    }
}
```

Better add constructor argument. filePath is configuration value.

```php
final class FileLogger
{
    private string filePath;
    
    public function __construct(string filePath)
    {
        this.filePath = filePath;
    }
    
    public function log(message): void
    {
        file_put_contents(this.filePath, message, FILE_APPEND);
    }
}
logger = new FileLogger('/var/log/app.log');
```

### Introduce constructor arguments to make behavior replaceable
When you feel the need to change the behavior of a service, look for ways to
make this behavior configurable through constructor arguments. If this isn’t an
option because you want to replace a larger piece of logic, look for ways to swap
out dependencies, which are also passed in as constructor arguments.
If the behavior you want to change isn’t represented by a dependency yet, extract
one by introducing an abstraction: a higher level concept and an interface. You
will then have a part you can replace, instead of modify.  
Abstraction offers the ability to compose and decorate behaviors, 
so they can become more complicated without the initial service knowing about it

Create abstraction by adding interfaces, create concrete implementation, then you can change them.

```php
interface FileLoader
{
    public function loadFile(string filePath): array
}

final class JsonFileLoader implements FileLoader
{
    public function loadFile(string filePath): array
    {
        Assertion.isFile(filePath);
        result = json_decode(file_get_contents(filePath), true);
        if (!is_array(result)) {
            throw new RuntimeException('Decoding "{filePath}" did not result in an array');
        }
        return result;
    }
}

final class XmlFileLoader implements FileLoader
{
    // ...
}
```

```php
final class ParameterLoader
{
    private FileLoader fileLoader;
    
    public function __construct(FileLoader fileLoader)
    {
        this.fileLoader = fileLoader;
    }
    
    public function load(filePath): array
    {
        rawParameters = this.fileLoader.loadFile(filePath);
        // ...
    }
}
    parameterLoader = new ParameterLoader(new JsonFileLoader());
    parameterLoader.load(__DIR__ . '/parameters.json');
    
    parameterLoader = new ParameterLoader(new XmlFileLoader());
    parameterLoader.load(__DIR__ . '/parameters.xml');
```

### Compose abstractions to achieve more complicated behavior

Use object composition

```php
final class MultipleLoaders implements FileLoader
{
    private array loaders;
    
    public function __construct(array loaders)
    {
        Assertion.allIsInstanceOf(loaders, FileLoader.className);
        Assertion.allIsString(array_keys(loaders));
        this.loaders = loaders;
    }
    
    public function loadFile(string filePath): array
    {
        extension = pathinfo(filePath, PATHINFO_EXTENSION);
        if (!isset(this.loaders[extension])) {
            throw new CouldNotLoadFile('There is no loader for file extension "{extension}"');
        }
        return this.loaders[extension].loadFile(filePath);
    }
}

parameterLoader = new ParameterLoader(
    new MultipleLoaders([
        'json' => new JsonFileLoader(),
        'xml' => new XmlFileLoader()])
    );
parameterLoader.load('parameters.json');
parameterLoader.load('parameters.xml');
```

Introducing the FileLoader abstraction is a smart thing to do,
but writing all these different loader implementations should be considered “generalization before it’s needed.”

### Decorate existing behavior

Decoration particular style of composition
Decoration is also often used when the cost of using the real service is somewhat high
“Wrapper” is the alternative nickname for the Decorator pattern
A wrapper is an object that can be linked with some target object.  
The wrapper contains the same set of methods as the target and delegates to it all requests it receives.
However, the wrapper may alter the result by doing something either before or after it passes the request to the target
The wrapper implements the same interface as the wrapped object.

```php
final class ReplaceParametersWithEnvironmentVariables implements FileLoader
{
    private FileLoader fileLoader;
    private array envVariables;
    public function __construct(FileLoader fileLoader, array envVariables)
    {
        this.fileLoader = fileLoader; // The real file loader is injected as a constructor argument.
        this.envVariables = envVariables;
    }
    
    public function loadFile(string filePath): array
    {
        parameters = this.fileLoader.loadFile(filePath); // We use the real file loader to load the file.
        foreach (parameters as key => value) {
            parameters[key] = this.replaceWithEnvVariable(value); // And do additional work
        }
        return parameters;
    }
    
    private function replaceWithEnvVariable(string value): string
    {
        if (isset(this.envVariables[value])) {
            return this.envVariables[value];
        }
        return value;
    }
}

parameterLoader = new ParameterLoader(
    new ReplaceParametersWithEnvironmentVariables(
        new MultipleLoaders(['json' => new JsonFileLoader(), 'xml' => new XmlFileLoader()]),
        ['APP_ENV' => 'dev',]
    )
);
```

### Use notification objects or event listeners for additional behavior
Event listeners as a technique for separating the main job of a command method from its secondary tasks
The advantage of using an event dispatcher is that it enables you to add new behavior
to a service without modifying its existing logic. Once it’s in place, an event dispatcher
offers the option to add new behavior. You can always register another listener/handler/subscriber for an
existing event.

### Don’t use inheritance to change an object’s behavior
Downsides of inheritance
- Subclass and parent class become tied together
- A lot of the internals of the object are now exposed (More methods and properties should be protected or public)

Inheritance template method pattern
```php
abstract class ParameterLoader
{
//Mark all properties “private” to keep them private to the parent class. 

    final public function load(filePath): array //Mark all methods “final” to make it impossible to override them.
    {
        parameters = [];
        foreach (/* ... */) {
            // ...
            if (/* ... */) {
                rawParameters = this.loadFile(filePath);
                // ...
            }
        }
        return parameters;
    }
    
    //Only allow one method to be implemented (not overridden).
    abstract protected function loadFile(string filePath): array; 
}
```

Everything that can be done with the template method pattern can also be achieved with composition
Inheritance should only be used to define a strict hierarchy of types.
However, some object types like entities or value objects don’t
support DI, so you can’t really achieve code reuse.
In that case trait can be used. Trait is plain code reuse a compiler-level copy/paste of code.

### Mark classes as final by default
The only exception to the rule is when you want to declare a hierarchy of objects.

### Mark methods and properties private by default
The only way in which clients can
interact with an object is by constructing it and calling public methods on it.
The freedom to change anything about the object’s internals, as long as it doesn’t break
the contract defined by its published interface, is a big win.