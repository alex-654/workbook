## 1.1. Simplifying Code

- Early abstractions are often not quite right.
- You can’t create the right abstraction until you fully understand the code, but the existence of the wrong abstraction
  may prevent you from ever doing so.

### Consistency

- Inconsistent styling makes code harder for humans to parse;
  Even when you type different type of if statement (exm. ternary and if).
- Combining many ideas into a small section of code makes it difficult to isolate and name any single concept
- Writing code is like writing a book; your efforts are for other readers.

Asking the following questions will give you insight into the potential expense of a bit of code:

1. How difficult was it to write?
2. How hard is it to understand?
3. How expensive will it be to change?

- Incomprehensible conciseness (code is short but not understandable) is clearly not the best solution.
- Code that answering the domain questions!
- The individual methods are easy to understand, but despite this, it’s tough to get a sense of the entire class.
- You should name methods not after what they do, but after what they mean, what they represent in the context of your
  domain.

- When you DRY out duplication or create a method to name a bit of code, you add levels of indirection that make it more
  abstract.
- In theory these abstractions make code easier to understand and change, but in practice they often achieve the
  opposite.
- One of the biggest challenges of design is knowing when to stop.

## 1.2. Judging Code

- Evaluating Code Based on Opinion
- or Evaluating Code Based on Facts

### 1.2.2 Evaluating Code Based on Facts

- Source Lines of Code SLOC, sometimes shortened to just LOC
- Cyclomatic Complexity
- Assignments, Branches and Conditions (ABC) Metric
    - Assignments is a count of variable assignments.
    - Branches of control, i.e., it counts function calls or message sends.
    - Conditions counts conditional logic (if statement)

## 2.1. Understanding Testing

Test-Driven Development by Example, Kent Beck -> Red/Green/Refactor cycle

When writing code consider - maximum understandability then changeability.

So, while it is important to consider the problem and to sketch
out an overall plan before writing the first test, don’t overthink it.

TDD tells you to write the simplest code that will pass this test.

When doing TDD, you toggle between wearing two hats. While in the
"writing tests" hat, you keep your eye on the big picture and
work your way forward with the overall plan in mind.
When in the "writing code" hat, you pretend to know nothing other
than the requirements specified by the tests at hand.   
Thus, although each individual test is correct, until are all written, the code is incomplete.

TDD requires that you pass tests by writing simple code.

“As the tests get more specific, the code gets more generic.”

So when making a test pass, you try to do so with transformations that are simpler (higher on the list) than those that
are more complex.

https://blog.cleancoder.com/uncle-bob/2013/05/27/TheTransformationPriorityPremise.html

- ({}–>nil) no code at all -> code that employs nil
- (nil->constant)
- (constant->constant+) a simple constant to a more complex constant
- (constant->scalar) replacing a constant with a variable or an argument
- (statement->statements) adding more unconditional statements.
- (unconditional->if) splitting the execution path
- (scalar->array)
- (array->container)
- (statement->recursion)
- (if->while)
- (expression->function) replacing an expression with a function or algorithm
- (variable->assignment) replacing the value of a variable.

During the green we prefer simpler transformations. During the red phase we prefer tests that can be passed with simpler
transformations.

Developing the habit of writing just enough code to pass the tests forces you to write better tests.

Tests are not the place for abstractions — they are the place for concretions.

## 3. Unearthing Concepts

If your tests are flawed such that they interfere with refactoring, improve them first, and then refactor.

The good news is that you don’t have to be able to see the
abstraction in advance. You can find it by iteratively applying a small set of simple rules.
These rules are known as "Flocking Rules":

1. Select the things that are most alike.
2. Find the smallest difference between them.
3. Make the simplest change that will remove that difference.

As you’re following the flocking rules:

- For now, change only one line at a time.
- Run the tests after every change.
- If the tests fail, undo and make a better change.

Difference holds the key to understanding. If two concrete
examples represent the same abstraction and they contain a
difference, that difference must represent a smaller
abstraction within the larger one. If you can name the
difference, you’ve identified that smaller abstraction.

- Name the concept, create the method, and replace the difference with a common message send.
- So anything that increases understandability lowers costs.
- Subclasses, by definition, are all that their superclasses are, plus more,

## 5. Separating Responsibilities

Having multiple methods that take
the same argument is a code smell. It’s important, however, to
recognize that here the term "same" means same concept, not
identical name. In an ideal world, each different concept
would have its own unique, precise name, and there would be
no ambiguity. Unfortunately, real world code often fails to
meet this ideal. In long-lived applications, the same concept
might go by several different names, or, as in this case,
different concepts might hide behind a single name. These
naming mistakes make it harder to notice underlying code
smells, and now that you’re looking for patterns in the code,
you must examine the arguments and clarify the abstractions
that they represent.

Testing for equality also
makes the code more precise, and this precision, as you will
soon see, enables future refactorings.

As an OO practitioner, when you see a conditional, the hairs
on your neck should stand up. You should feel entitled to send
messages to objects, and look for a way to write code that
allows you to do so. The above pattern means that objects are
missing, and suggests that subsequent refactorings are needed
to reveal them. Be on the lookout for this code shape, as it
implies that there’s more to be done.
This is not to say that you’ll never have a conditional in an
object-oriented application. There is a place for conditionals in
OO. Manageable OO applications consist of pools of small
objects that collaborate to accomplish tasks. Collaborators
must be brought together in useful combinations, and
assembling these combinations requires knowing which
objects are suitable. Some object, somewhere, must choose
which objects to create, and this often involves a conditional.
However, there’s a big difference between a conditional that
selects the correct object and one that supplies behavior. The
first is acceptable and generally unavoidable. The second
suggests that you are missing objects in your domain.