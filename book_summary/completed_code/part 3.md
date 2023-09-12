## 10 General Issues in Using Variables

### 10.2 Making Variable Declarations Easy

Declare all variables. Don't use implicit declarations.

### 10.3 Guidelines for Initializing Variables

- Initialize each variable close to where it’s first used
  This is an example of the Principle of Proximity: keep related actions together

```
Dim accountIndex As Integer
accountIndex = 0
' code using accountIndex
...

Dim total As Double
total = 0.0
' code using total
...
```

Another problem with the first approach is that throwing all the initializations
together creates the impression that all the variables are used throughout the whole
routine when in fact done is used only at the end.
More error-prone specially on code modification

- Ideally, declare and define each variable close to where it’s first used
- Use final or const when possible
- Initialize a class’s member data in its constructor

### 10.4 Scope

“Scope” (visibility) is a way of thinking about a variable’s celebrity status: how famous is it?

**Localize References to Variables** and
**Keep Variables “Live” for as Short a Time as Possible**

- It reduces the window of vulnerability
- Person reading your code to focus on one section at a time (you can concentrate on a smaller section of code).  
  If the references are far apart, you force the reader to jump around in the program.
- Code more readable
- Debugging more simple
- Extract new methods more easy

### 10.6 Binding Time

“binding time”: the time at which the variable and its value are bound together
In general, the later you make the binding time, the more flexibility you build into your code.

Example of a Variable That’s Bound at Code-Writing Time

```
titleBar.color = 0xFF; // 0xFF is hex value for color blue
```

Java Example of a Variable That’s Bound at Compile Time

```
private static final int COLOR_BLUE = 0xFF;
private static final int TITLE_BAR_COLOR = COLOR_BLUE;
...
titleBar.color = TITLE_BAR_COLOR;
```

Java Example of a Variable That’s Bound at Run Time

```
titleBar.color = ReadTitleBarColor();
```

### 10.8 Using Each Variable for Exactly One Purpose

## Fundamental Data Types

### 12.2 Integers

- Check for integer division
- Check for integer overflow

```
Signed 8-bit -128 through 127
Unsigned 8-bit 0 through 255
Signed 16-bit -32,768 through 32,767
Unsigned 16-bit 0 through 65,535
Signed 32-bit -2,147,483,648 through 2,147,483,647
Unsigned 32-bit 0 through 4,294,967,295
Signed 64-bit -9,223,372,036,854,775,808 through 9,223,372,036,854,775,807
Unsigned 64-bit 0 through 18,446,744,073,709,551,615
```

### 12.3 Floating-Point Numbers

Nonterminating decimals like 1/3 or 1/7 can usually be represented to
only 7 or 15 digits of accuracy

- Avoid additions and subtractions on numbers that have greatly different magnitudes
- Avoid equality comparisons

```php
$a = 1.23456789;
$b = 1.23456780;
$epsilon = 0.00001;

if(abs($a-$b) < $epsilon) {
    echo "true";
}
```

- Anticipate rounding errors
    - Change to a variable type that has greater precision
    - Change to binary coded decimal (BCD) variables
    - Change from floating-point to integer variables

### 12.4 Characters and Strings

- Avoid magic characters and strings
- Know how your language and environment support Unicode In some languages
  such as Java, all strings are Unicode. In others such as C and C++, handling Unicode
  strings requires its own set of functions
- Decide on an internationalization/localization strategy early in the lifetime of a program

### 12.5 Boolean Variables

### 12.6 Enumerated Type

- Use enumerated types for readability
- Reliability (type checking)
- Modifiability. Enumerated types make your code easy to modify
- Alternative to boolean variables (Status_Success, Status_Warning, and Status_FatalError)
- Define the first and last entries of an enumeration for use as loop limits

### 12.7 Named Constants

### 12.8 Arrays

Some of the brightest people in computer science have suggested that
arrays never be accessed randomly, but only sequentially. Their argument is that random accesses in arrays are similar
to random gotos in a program: such accesses tend to be undisciplined, error-prone, and hard to prove correct.
Consider using container classes that you can access sequentially—sets, stacks, queues, and so on—as alternatives before
you automatically choose an array.

### 12.9 Creating Your Own Types (Value object)
Programmer-defined data types are one of the most powerful capabilities a language
can give you to clarify your understanding of a program.
 reasons for create 
- To make modifications easier
- To avoid excessive information distribution (information-hiding/encapsulation)

## Unusual Data Types

### 13.1 Structures

### 13.3 Global Data