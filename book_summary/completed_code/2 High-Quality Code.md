# Design in Construction

“Design” might be just writing a class interface in pseudocode before writing the details. It might be drawing diagrams
of a few class relationships before coding them.  
It might be asking another programmer which design pattern seems like a better choice. Regardless of how it’s done,
small projects benefit from careful design just as larger projects do, and recognizing design as an explicit activity
maximizes the benefit you will receive from it.

## 5.1 Design Challenges

Design is the activity that links requirements to coding and debugging.

**Design Is a Wicked Problem**  
Example with Tacoma Narrows bridge.
Until the bridge collapsed, its engineers didn’t know that aerodynamics needed to be considered to such an extent.

You have to “solve” the problem once in order to clearly define it and then solve it again to create a solution that
works.

**Design Is a Sloppy Process (Even If it Produces a Tidy Result)**  
Design is sloppy because you take many false steps and go down many blind alleys. Indeed,
making mistakes is the point of design it’s cheaper to make mistakes and correct designs.

How much detail is enough? -> “When you’re out of time.”

**Design Is About Tradeoffs and Priorities**  
Response rate or minimizing development time? a designer will choose one design.

**Design Involves Restrictions**

**Design Is Nondeterministic**

**Design Is a Heuristic Process**  
Design techniques tend to be heuristics — “rules of thumb” or “things to try that sometimes work” rather than repeatable
processes that are guaranteed to produce predictable results.

**Design Is Emergent**  
Designs don’t spring fully formed directly from someone’s brain. They evolve and improve through design reviews,
informal discussions, experience writing the code itself, and experience revising the code.

## 5.2 Key Design Concepts

**Importance of Managing Complexity**  
At the software-architecture level, the complexity of a problem is reduced by dividing the system into subsystems.
Humans have an easier time comprehending several simple pieces of information than one complicated piece.  
The bottom line is that programmers who compensate for inherent human limitations write code that’s easier for
themselves and others to understand and that has fewer errors.

**Desirable Characteristics of a Design**  
Here’s a list of internal design characteristics:

- Minimal complexity
- Ease of maintenance
- Loose coupling  
  means designing so that you hold connections among different parts of a program to a minimum.
- Extensibility  
  You can change a piece of a system without affecting other pieces.
- Reusability
- High fan-in  
  refers to having a high number of classes that use a given class. High fan-in implies that a system has been designed
  to make good use of utility
  classes at the lower levels in the system.
- Low-to-medium fan-out  
  means having a given class use a low-to-medium number of other classes. High fan-out (more than
  about seven) indicates that a class uses a large number of other classes and may therefore be overly complex.
- Portability
- Leanness  
  Voltaire said that a book is finished not when nothing more can be added but when nothing more can be taken away.
- Stratification  
  Design the system so that you can view it at one level without dipping into other levels.
- Standard techniques  
  Try to give the whole system a familiar feeling by using standardized, common approaches.

## 5.3 Design Building Blocks: Heuristics

- **Find Real-World Object**  
  object-oriented approach, which focuses on identifying real-world and synthetic objects
- **Form Consistent Abstractions**  
  Abstraction is the ability to engage with a concept while safely ignoring some of its
  details—handling different details at different levels.   
  If you refer to an object as a “house” rather
  than a combination of glass, wood, and nails, you’re making an abstraction.
- **Encapsulate Implementation Details**  
  Encapsulation picks up where abstraction leaves off.  
  Abstraction says, “You’re allowed to look at an object at a high level of detail.”  
  Encapsulation says, “Furthermore, you aren’t allowed to look at an object at any other level of detail.”
- **Inherit** - When Inheritance Simplifies the Design
- **Hide Secrets** (Information Hiding)  
  Asking about what needs to be hidden supports good design decisions at all levels.
  Two types of hiding
    - Hiding complexity so that your brain doesn't have to deal with it
    - Hiding sources of change so that when change occurs, the effects are localized

**Identify Areas Likely to Change**  
The goal is to isolate unstable areas so that the effect of a change will be limited to one routine, class, or package

- **Identify** items that seem likely to change.
- **Separate** items that are likely to change
- **Isolate** (create interface) items that seem likely to change.

To find most changed files on git repo - https://rmcreative.ru/blog/post/git-churn

**Keep Coupling Loose**  
Good coupling between modules is loose enough that one module can easily be used by other modules

**Aim for Strong Cohesion**  
Central purpose how focused the class is. Similar for Single Responsibility.  
Cohesion is a useful tool for managing complexity because the more that code in a class supports a central purpose, the
more easily your brain can remember everything the code does.

**Design for Test**

**Draw a Diagram**  
A picture is worth 1000 words—kind of.

## 5.4 Design Practices

- Iterate  
  Design is an iterative process. You don’t usually go from point A only to point B; you
  go from point A to point B and back to point A.  
  Understand the problem, devise a plan,
  carry out the plan, and then look back to see how you did
- Divide and Conquer
- Top-Down and Bottom-Up Design Approaches
  The key difference between top-down and bottom-up strategies is that one is a decomposition strategy and the other is
  a composition strategy. One starts from the general problem and breaks it into manageable pieces; the other starts
  with manageable pieces and builds up a general solution.  
  To summarize, top down tends to start simple, but sometimes low-level complexity
  ripples back to the top, and those ripples can make things more complex than they
  really needed to be. Bottom up tends to start complex, but identifying that complexity
  early on leads to better design of the higher-level classes.
  In the final analysis, top-down and bottom-up design aren’t competing strategies they’re mutually beneficial.  
  Design is a heuristic process, which means that no solution is guaranteed to work every time.
- Experimental Prototyping
  works poorly when developers aren’t disciplined about writing the absolute minimum of code needed to answer a
  question.  
  Prototyping also works poorly when the design question is not specific enough. A
  design question like “Will this database framework work?” does not provide enough direction for prototyping.
- Collaborative Design  
  Two heads are often better than one.
- Capturing Your Design Work
  Insert design documentation into the code itself

## 5.5 Comments on Popular Methodologies

But while you can’t know the exact right amount of design with any confidence, two amounts of design are guaranteed to
be wrong every time: designing every last detail and not designing anything at all.

# Working Classes

## 6.1 Class Foundations: Abstract Data Types (ADTs)

Abstract data types are exciting because you can use them to manipulate real-world entities rather
than low-level, implementation entities. Instead of inserting a node into a linked list,
you can add a cell to a spreadsheet

## 6.2 Good Class Interfaces

- Present a consistent level of abstraction in the class interface
  Each class should implement one and only one ADT

Exm. mixed abstraction

```
class Employee
void AddEmployee( Employee employee );
void RemoveEmployee( Employee employee );

Employee NextItemInList();
Employee FirstItem();
Employee LastItem()
```

Better

```
class Employee
void AddEmployee( Employee employee );
void RemoveEmployee( Employee employee );
Employee NextEmployee();
Employee FirstEmployee();
Employee LastEmployee();
```

- Be sure you understand what abstraction the class is implementing
  ATD Grid has 15 methods; ATD spreadsheet has 150 methods
  If grid class need just one method from spreadsheet, don't implement and show all 150 methods
- Provide opposite methods
  Don't make methods with bool params make two (turnOn, turnOff)
- Move unrelated information to another class
  In some cases, you’ll find that half a class’s routines work with half the class’s data and half the routines work
  with the other half of the data. In such a case, you really have two classes masquerading as one.
  Break them up!
- Semantic violations
  Don’t make assumptions about the class’s users. Make interfaces programmatic rather than semantic when possible.  
  The semantic part of the interface consists of the assumptions about how the interface
  will be used, which cannot be enforced by the compiler. The semantic interface includes
  considerations such as “RoutineA must be called before RoutineB”
- Beware of erosion of the interface’s abstraction under modification
- Consider abstraction and cohesion together
  I have found that focusing on the abstraction presented by the class interface tends to
  provide more insight into class design than focusing on class cohesion.
- Good Encapsulation
    - Minimize accessibility of classes and members
    - Don’t expose member data in public.  
      Abstraction and encapsulation are related because, without encapsulation, abstraction tends to
      break down. In my experience, either you have both abstraction and encapsulation or
      you have neither. There is no middle ground.
      With true encapsulation, programmers would not be able to see implementation details at all
- Favor read-time convenience to write-time convenience.  
  Favoring a technique that speeds write-time convenience at the expense of read-time convenience is a false economy

## 6.3 Design and Implementation Issues

Law of Demeter
"use only one dot" - That is, the code a.m().n() breaks the law where a.m() does not.

- Each unit should have only limited knowledge about other units: only units "closely" related to the current unit.
- Each unit should only talk to its friends; don't talk to strangers.
- Only talk to your immediate friends.

Avoid classes named after verbs.

Key points:

- Class interfaces should provide a consistent abstraction.
- A class interface should hide something — a system interface, a design decision,
  or an implementation detail.
- Inheritance is a useful tool, but it adds complexity
- Classes are your primary tool for managing complexity. Give their design as
  much attention as needed to accomplish that objective

## 7 High-Quality Routines(Methods)

### 7.1 Valid Reasons to Create a Routine

Reasons to create a method

- Isolate and Reduce complexity
- Hide implementation details
- Limit effects of changes
- Hide global data
- Make central points of control
- Facilitate reusable code
- Introduce an intermediate, understandable abstraction
- Avoid duplicate code
- Hide sequences  
  It’s a good idea to hide the order in which events happen to be processed.

### 7.2 Design at the Routine Level

The goal is to have each routine do one thing well and not do anything else

7.3 Good Routine Names

- Describe everything the routine does
  exm ComputeReportTotalsAndOpenOutputFile()
- Avoid meaningless, vague, or wishy-washy verbs
  like HandleCalculation(), PerformServices(), OutputUser(), ProcessInput(), and DealWithOutput()
- Make names of routines as long as necessary
- Use opposites precisely like show/hide, source/target, next/previous, create/destroy, insert/delete
- Establish conventions for common operations

### 7.4 How Long Can a Routine Be?

Let issues such as the routine’s cohesion, depth of nesting, number
of variables, number of decision points, number of comments dictate the length of the routine.
But consider the routine should be allowed to grow organically up to 100–200 lines

## The Pseudocode Programming Process

### 9.1 Summary of Steps in Building Classes and Routines

Steps in Creating a Class

- Create a general design for the class
- Construct each routine within the class
- Review and test the class as a whole

Steps in Building a Routine

Experts have developed numerous approaches to creating routines

- Pseudocode Programming Process
- Test-Driven Development
- Refactoring
- Design by contract

Example of Good Pseudocode

```
Keep track of current number of resources in use
If another resource is available
  Allocate a dialog box structure
  If a dialog box structure could be allocated
    Note that one more resource is in use
    Initialize the resource
    Store the resource number at the location provided by the caller
  Endif
Endif
Return true if a new resource was created; else return false
```

Key points

- Constructing classes and constructing routines tends to be an iterative process.
  Insights gained while constructing specific routines tend to ripple back through
  the class’s design.
- Writing good pseudocode calls for using understandable English, avoiding features specific to a single programming
  language, and writing at the level of intent (describing what the design does rather than how it will do it).
- The Pseudocode Programming Process is a useful tool for detailed design and
  makes coding easy. Pseudocode translates directly into comments, ensuring
  that the comments are accurate and useful.
- Don’t settle for the first design you think of. Iterate through multiple approaches
  in pseudocode and pick the best approach before you begin writing code.
- Check your work at each step, and encourage others to check it too. That way,
  you’ll catch mistakes at the least expensive level, when you’ve invested the least
  amount of effort.

