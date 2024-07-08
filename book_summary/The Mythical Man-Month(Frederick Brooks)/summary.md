## 1 The Tar Pit

A programming systems product takes about nine times
as much effort as the component programs written separately for private use.

The Joys of the Craft of programming

- making things
- making things that are useful to other people
- solve complex puzzle-like
- always learning
- delight of working in such a tractable medium. Programmer builds his castles in the air,
  from air, creating by exertion of the imagination.

The Woes of the Craft

- First, one must perform perfectly. Adjusting to the requirement
  for perfection is, I think, the most difficult part of learning to.
- other people set one's objectives, provide one's resources, and furnish one's information.
- He depends upon other people's programs. These are often maldesigned, poorly implemented, incompletely delivered (no
  source code or test cases), and poorly documented. So he must spend hours studying and fixing
  things that in an ideal world would be complete, available, and
- With any creative activity come dreary hours of tedious, painstaking labor, and programming is no
  exception.
- The product over which one has labored so long appears to be obsolete upon (or before) completion.

## 2 The Mythical Man-Month

- More programming projects have gone away for lack of calendar time than for all other causes combined.
- All programmers are optimists.
- If task can be partitioned added more people can lead to more speed in project, but with correction on
  training and intercommunication.
- When added people to project communication links grows not linear more exponentially
- Better add people at start of project then at the end.

- Brooks's Law (Oversimplifying):  
  **Adding manpower fo a late software project makes it later.**

- Adding people to a software project increases the total effort necessary in three ways: the work and disruption of
  repartitioning itself, training the new people, and added intercommunication.

## 3 The Surgical Team

- Best and worst programmer performances averaged about 10:1 on productivity measurements and an amazing 5:1 on
  program speed and space measurements!
- Better use one super mind then 10 average worker.
- Performance can vary on command organization. One of way to organize is Surgical Team.
- A team of two, with one leader, is often the best use of minds.
- A small sharp team is too slow for really big systems.

## 4 Aristocracy, Democracy, and System Design

### Conceptual Integrity

- Metaphor of building ancient cathedrals that require couple of builders generation.
  In different age they add new modern things that doesn't pair look good with overall concept.
- Conceptual integrity of a system determines its ease of use.
- Conceptual integrity in turn dictates that the design must proceed
  from one mind, or from a very small number of agreeing resonant minds.
- The separation of architectural effort from implementation is
  a very powerful way of getting conceptual integrity on very large projects.
- In an **unconstrained implementing group**, most thought and debate goes into architectural
  decisions, and implementation proper gets short shift.
- A conceptually integrated system is faster to build and to test.

Architecture must be carefully distinguished from implementation. As Blaauw has said, "Where architecture tells what
happens, implementation tells how it is made to happen."   
He gives as a simple example a clock, whose architecture consists of the face, the hands, and the winding knob.  
When a child has learned this architecture, he can tell time as easily from a wristwatch as
from a church tower.  
The implementation, however, and its realization, describe what goes on inside the case-powering by any of many
mechanisms and accuracy control by any of many.

## 5 The Second-System Effect

The general tendency is to over-design the second system, using all the ideas and frills that were cautiously
sidetracked on the first one.  
When he does his third and later ones, his prior experiences will confirm each other as to the general characteristics
of such systems, and their differences will identify those parts of his experience that are particular and not
generalizable.

## 6 Passing the Word

Assuming that architect is disciplined, experienced architects and
that there are many implementers, how shall the manager ensure
that everyone hears, understands, and implements the architects decisions?  
How can a group of 10 architects maintain the conceptual integrity of a system which 1000 men are building?

- Even when a design team is large, the results must be reduced to writing by one or two, in order that the
  mini-decisions be consistent.
- It is important to explicitly define the parts of an architecture that are not prescribed as carefully as those that
  are.
- One needs both a formal definition of a design, for precision, and a prose definition for comprehensibility.
- One of the formal and prose definitions must be standard, and the other derivative.
- An architectural "definition will be cleaner and the architectural discipline tighter if at least two
  implementations are built initially."

### Formal Definitions

I think we will see future specifications to consist of both a formal definition and a prose definition.
An ancient adage warns, -  
_"Never go to sea with two chronometers; take one or three."_   
The same thing clearly applies to prose and formal definitions.

## 7 Why Did The Tower of Babel Fail?

The Tower of Babel project failed because of lack of communication and of its consequent, organization.

### Communication in the Large Programming Project

So it is today. Schedule disaster, functional misfits, and system bugs all arise because the left hand doesn't know what
the right hand is doing. Teams drift apart in assumptions.  
How, then, shall teams communicate with one another? -> In as many ways as possible.

- Informally. Good telephone service and a clear definition of intergroup dependencies will encourage the hundreds of
  calls upon which common interpretation of written documents depends.
- Meetings. Regular project meetings, with one team after another giving technical briefings, are invaluable. Hundreds
  of minor misunderstandings get smoked out this way.
- Workbook. A formal project workbook must be started at the beginning.

### The Project Workbook

All the documents of the project need to be part of this structure. This includes objectives, external specifications,
interface specifications, technical standards, internal specifications, and administrative memoranda.

### Organization

- The purpose of organization is to reduce the amount of
  communication and coordination necessary.
- Organization embodies division of labor and specialization of
  function in order to obviate communication.
- The conventional tree organization reflects the authority
  structure principle that no person can serve two masters.
- The communication structure in an organization is a network, not a tree,
- Every subproject has two leadership roles to be filled, that
  of the producer and that of the technical director, or architect.
  The functions of the two roles are quite distinct and require different talents.

## 8 Calling the Shot

- Accurately estimate the total effort or schedule of a programming project is difficult.
- Time on you pet project is not applicable to real production project

## 9 Ten Pounds in a Five-Pound Sack

This chapter looks outdated. More about wise system resource usage.

## 10 The Documentary Hypothesis

No matter how small the project, however, the manager is wise to begin immediately to formalize at least mini-documents
to serve as his database.

- First, writing the decisions down is essential. Only when one writes do the gaps and inconsistencies appear. The act
  of writing turns out to require hundreds of mini-decisions, and it is the existence of these that distinguishes clear,
  exact policies from fuzzy ones.
- Second, the documents will communicate the decisions to others. The manager will be continually amazed that policies
  he took for common knowledge are totally unknown by some member of his team. Since his fundamental job is to keep
  everybody going in the same direction, his chief daily task will be communication, not
  decision-making, and his documents will immensely lighten this load.
- Finally, a manager's documents give him a database and checklist. By reviewing them periodically he sees where he is,
  and he sees what changes of emphasis or shifts in direction are needed.
- The task of the manager is to develop a plan and then to realize it. But only the written plan is precise and
  communicable.

## 11 Plan to Throw One Away

### Pilot Plants and Scaling Up

Chemical engineers learned long ago that a process that works in the laboratory cannot be implemented in a factory in
only one step.   
An intermediate step called the pilot plant (beta version) is necessary to give experience in scaling quantities up and
in operating in nonprotective environments. For example, a laboratory process for desalting water will be tested in a
pilot plant of 10,000 gallon/day capacity before being used for a 2,000,000 gallon/day community water system.

In most projects, the first system built is barely usable. It may be too slow, too big, awkward to use, or all three.
There is no alternative but to start again, starting but smarter, and build a redesigned version in which these problems
are solved.
The discard and redesign may be done in one lump, or piece-by-piece, but it will be done.

### The Only Constancy Is Change Itself

- "The programmer delivers satisfaction of a user need rather than any tangible product." (Cosgrove)
- Both the actual need and the user's perception of that need will change as programs are built, tested, and used.
- Reason why people not like documented -
  "Programmer reluctance to document designs comes not
  so much from laziness as from the hesitancy to undertake
  defense of decisions that the designer knows are tentative". (Cosgrove)
- Management structures also need to be changed as the system changes.

### One Step Forward and One Step Back

- Lehman and Belady have studied the history of successive releases in a large operating system. They find that the
  total number of modules increases linearly with release number, but that the number of modules affected increases
  exponentially with release number.
- All repairs tend to destroy the structure, to increase the entropy and disorder of the system. Less and less effort is
  spent on fixing original design flaws; more and more is spent on fixing flaws introduced by earlier fixes.
- Maintenance cost is strongly affected by the number of users. More users find more bugs.
- Fixing a defect has a substantial (20-50 percent) chance of introducing another.

## 12 Sharp Tools

_A good workman is known by his tools._ proverb

## 13 The Whole and the Parts

- Conceptual integrity of the product not only makes it easier to use, it also makes it easier to build and less subject
  to bugs.
- Testing the specification. Long before any code exists, the specification must be handed to an outside testing group
  to be scrutinized for completeness and clarity.
- **Top-down design.**  
  Each of the architecture, implementation, and realization can be best done by top-down methods.

## 14 Hatching a Catastrophe

"How does a project get to be a year late? . . . One day at a time."

- How does one control a big project on a tight schedule? The first step is to have a schedule.
- Each of a list of events, called milestones, has a date. Picking the dates is an estimating problem.
- For picking the milestones there is only one relevant rule.
  Milestones must be concrete, specific, measurable events, defined with knife-edge sharpness.
  To supplement Sophocles, no one enjoys bearing bad news, either, so it gets softened without any real intent to
  deceive.
- Chronic schedule slippage is a morale-killer. Jim Mc-Carthy of Microsoft says, "If you miss one deadline, make
  sure you make the next one".
- It is necessary to have review techniques by which the true status is made known, whether cooperatively or not.
- As we have seen, one must get excited about a one-day slip. Such are the elements of catastrophe.

A baseball manager recognizes a nonphysical talent, hustle, as an essential gift of great players and great teams. It is
the characteristic of running faster than necessary, moving sooner than necessary, trying harder than necessary.  
It is essential for great programming teams, too.  
Hustle provides the cushion, the reserve capacity, that enables a team to cope with routine mishaps, to anticipate and
forfend minor calamities.

## 15 The Other Face

- A computer program is a message from a man to a machine.
- But a written program has another face, that which tells its story to the human user.
- Memory will fail. To use a program every user needs a prose description of the program.
- Most documentation fails in giving too little overview.
  The trees are described, the bark and leaves are commented, but there is no map of the forest.
- Self-Documenting Programs+