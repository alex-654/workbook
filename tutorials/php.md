### Magic methods

17 methods -  
__construct(), __destruct(),   
__sleep(), __wakeup(), __serialize(), __unserialize(), __set_state(),  
__invoke(),
__clone(),  
__call(), __callStatic(),  
__get(), __set(), __isset(), __unset(),
__toString(), __debugInfo()

All magic methods, with the exception of __construct(), __destruct(), and __clone(), must be declared as public

### __sleep() | __wakeup() | __serialize() and __unserialize()

function serialize()/unserialize() checks if class have methods

- __sleep() / __wakeup() (appear in php 4)
- __serialize() and __unserialize() (appear in php 7.4 to resolve issues from __sleep and Serializable interface)

If __serialize() and __sleep() or object implements the Serializable interface, only __serialize() will be called.  
Php object serializable by default. But for resources, callables you need to handle it manually.
And maybe helpful with big object graph to cut some parts.

```php
class A {
    private $prop_a;
    public function __serialize(): array {
        return ["prop_a" => $this->prop_a];
    }
    public function __unserialize(array $data) {
        $this->prop_a = $data["prop_a"];
    }
}
class B extends A {
    private $prop_b;
    public function __serialize(): array {
        return [
            "prop_b" => $this->prop_b,
            "parent_data" => parent::__serialize(),
        ];
    }
    public function __unserialize(array $data) {
        parent::__unserialize($data["parent_data"]);
        $this->prop_b = $data["prop_b"];
    }
}
```

### __set_state()

https://stackoverflow.com/questions/46441958/what-is-the-real-purpose-of-magic-method-set-state-in-php
This static method is called for classes exported by var_export().

```php
class A
{
    public $var1;
    public $var2;

    public static function __set_state($an_array)
    {
        $obj       = new A;
        $obj->var1 = $an_array['var1'];
        $obj->var2 = $an_array['var2'];

        return $obj;
    }
}

$a       = new A;
$a->var1 = 5;
$a->var2 = 'foo';

$serializedObject = var_export($a, true);
//$serialize = A::__set_state(array('var1' => 5, 'var2' => 'foo',))
eval('$unserializedObject = ' . $serializedObject . ';');

```

So the real purpose of __set_state method is to initialize an object in case someone "serialized" it with var_export.

### __invoke() (just because you can, doesn't mean you should)

The __invoke() method is called when a script tries to call an object as a function.  
https://stackoverflow.com/questions/888064/php-5-3-magic-method-invoke

```php
class CallableClass
{
    public function __invoke($x)
    {
        var_dump($x);
    }
}
$obj = new CallableClass;
$obj(5);
var_dump(is_callable($obj))
//output
//int(5)
//bool(true)
```

### __debugInfo()

This method is called by var_dump() when dumping an object to get the properties that should be shown. If the method
isn't defined on an object, then all public, protected and private properties will be shown.

### __clone()

An object copy is created by using the clone keyword (which calls the object's __clone() method if possible).

```php
$copy_of_object = clone $object;
```

When an object is cloned, PHP will perform a shallow copy of all of the object's properties. Any properties that are
references to other variables will remain references.
Main purpose to check for references object and clean some unnecessary data

yii examples

```php
    /**
     * This method is called after the object is created by cloning an existing one.
     * It removes all behaviors because they are attached to the old object.
     */
    public function __clone()
    {
        $this->_events = [];
        $this->_eventWildcards = [];
        $this->_behaviors = null;
    }
```

```php
    public function __clone()
    {
        if (is_object($this->query)) {
            $this->query = clone $this->query;
        }

        parent::__clone();
    }
```

### __call(), __callStatic()

__call() is triggered when invoking inaccessible methods in an object context.  
__callStatic() is triggered when invoking inaccessible methods in a static context.

example from yii

```php
    /**
     * Calls the named method which is not a class method.
     * This method will check if any attached behavior has
     * the named method and will execute it if available.
     * @param string $name the method name
     * @param array $params method parameters
     * @return mixed the method return value
     * @throws UnknownMethodException when calling unknown method
     */
    public function __call($name, $params)
    {
        $this->ensureBehaviors();
        foreach ($this->_behaviors as $object) {
            if ($object->hasMethod($name)) {
                return call_user_func_array([$object, $name], $params);
            }
        }
        throw new UnknownMethodException('Calling unknown method: ' . get_class($this) . "::$name()");
    }
```

### __get(), __set(), __isset(), __unset()

Property overloading/overriding

- __set() is run when writing data to inaccessible (protected or private) or non-existing properties.
- __get() is utilized for reading data from inaccessible (protected or private) or non-existing properties.
- __isset() is triggered by calling isset() or empty() on inaccessible (protected or private) or non-existing
  properties.
- __unset() is invoked when unset() is used on inaccessible (protected or private) or non-existing properties.

### PHPUnit’s Mock objects (YAGNI)

PHPUnit can mock interfaces

```php
<?php
class BankAccountTest extends PHPUnit_Framework_TestCase
{
  public function testSendEmailWhenReceivingMoney()
  {
    $twitter = $this->getMock('Twitter');
    $twitter->expects($this->once())
      ->method('tweet');
    $account = new BankAccount($twitter);
    $account->deposit(10);
  }
}

interface Twitter
{
  function tweet($message);
}

```

- We can test separate main code
- We can postpone realization of interface implementation
- We don't need all features of interface (YAGNI)

### static vs self

only matter

- in static context
- with inheritance

Static keyword comes with php 5.3
You can see many usage case in AR implementation.

- self:: realized in AST build (compile time) early binding. Should be a little faster than static. If inherited it will
  still represent the class where it was originally defined. Self could be replaced with class name.
- static:: - Late binding (in runtime)

Examples

```php
class A
{
    public static function getNameWithStatic()
    {
        return static::class;
    }

    public static function getNameWithSelf()
    {
        return self::class;
    }
    
    public static function newSelf()
    {
        return new self();
    }

    public static function newStatic()
    {
        return new static();
    }   
    
    public function getName()
    {
        echo self::class;  // it's like A::class no matter if it parent or child calls 
    } 
}

class B extends A
{
}

$resultSelf   = B::getNameWithSelf(); // A
$resultStatic = B::getNameWithStatic(); // B

$resultNewSelf = B::newSelf(); // A
$resultNewStatic = B::newStatic(); // B

$object = new B();
$object->getName(); // output A with self
```
