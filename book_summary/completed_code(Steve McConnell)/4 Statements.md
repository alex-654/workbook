## Organizing Straight-Line Code

### 14.1 Statements That Must Be in a Specific Order

In this case, ComputeMarketingExpenseAndInitializeMemberData() would be an adequate name. You might say it’s a terrible
name
because it’s so long, but the name describes what the routine does and is not terrible.
The routine itself is terrible!

### 14.2 Statements Whose Order Doesn’t Matter

As a general principle, make the program read from top to bottom rather than jumping around.
Bad

```
travelData.ComputeQuarterly();
salesData.ComputeQuarterly();
marketingData.ComputeQuarterly();
salesData.ComputeAnnual();
marketingData.ComputeAnnual();
travelData.ComputeAnnual();
salesData.Print();
travelData.Print();
marketingData.Print();
```

Good

```
SalesData salesData;
salesData.ComputeQuarterly();
salesData.ComputeAnnual();
salesData.Print();

TravelData travelData;
travelData.ComputeQuarterly();
travelData.ComputeAnnual();
travelData.Print();
```

- References to each object are kept close together;
- The number of lines of code in which the objects are “live” is small

## Using Conditionals

### 15.1 if Statements

- Write the nominal path through the code first; then write the unusual cases
- Put the normal case after the if rather than after the else
- Simplify complicated tests with boolean function calls
- In case. Use the default clause to detect errors

## Controlling Loops

### 16.1 Selecting the Kind of Loop

foreach Loop It has the advantage of eliminating loop-housekeeping arithmetic and
therefore eliminating any chance of errors in the loop-housekeeping arithmetic.

### 16.2 Controlling the Loop

- Treat the inside of the loop as if it were a function keep as much of the control as possible outside the loop
- Think of a loop as a black box: the surrounding program knows the control conditions but not the contents.
- Put initialization code directly before the loop. The Principle of Proximity advocates putting related statements
  together
- Make each loop perform only one function
- Use break and continue only with caution.  
  Use of break eliminates the possibility of
  treating a loop as a black box. Limiting yourself to only one statement to control a
  loop’s exit condition is a powerful way to simplify your loops. Using a break forces the
  person reading your code to look inside the loop for an understanding of the loop control
- Use meaningful names to avoid loop-index cross-talk

## Unusual Control Structures

## Table-Driven Methods (decision-table lookup)

Virtually anything you can select with logic statements, you can select with tables (arrays) instead. In simple cases,
logic statements are easier and more direct. As the logic chain becomes more complex, tables become increasingly
attractive.

## General Control Issues

### 19.1 Boolean Expressions

Forming Boolean Expressions Positively
Apply DeMorgan’s Theorems to simplify boolean tests with negatives
Table 19-1 Transformations of Logical Expressions Under DeMorgan’s Theorems

```
Initial Expression Equivalent Expression
not A and not B    not ( A or B )
not A and B        not ( A or not B )
A and not B        not ( not A or B )
A and B            not ( not A or not B )
not A or not B     not ( A and B )
not A or B         not ( A and not B )
A or not B         not ( not A and B )
A or B             not ( not A and not B )
```

### 19.4 Taming Dangerously Deep Nesting

More generally, complicated code is a sign that you don’t understand your program
well enough to make it simple. Deep nesting is a warning sign that indicates a need to
break out a routine or redesign the part of the code that’s complicated.

### 19.5 A Programming Foundation: Structured Programming

The term “structured programming” originated in a landmark paper, “Structured Programming,” presented by Edsger Dijkstra

Structured programming is a simple idea that is still relevant: you can build any
program out of a combination of sequences, selections, and iterations.

The Three Components of Structured Programming

- Sequence
- Selection (if-then-else)
- Iteration (loops)

### 19.6 Control Structures and Complexity

How to Measure Complexity
Perhaps the most influential of the numeric techniques is
Tom McCabe’s, in which complexity is measured by counting the number of “decision points” in a routine

Techniques for Counting the Decision Points in a Routine  
1 Start with 1 for the straight path through the routine.  
2 Add 1 for each of the following keywords, or their equivalents: if while repeat for
and or  
3 Add 1 for each case in a case statement.

Here’s an example:
if ( ( (status = Success) and done ) or
( not done and ( numLines >= maxLines ) ) ) then ...
In this fragment, you count 1 to start, 2 for the if, 3 for the and, 4 for the or, and 5 for
the and. Thus, this fragment contains a total of five decision points.

- 0–5 The routine is probably fine.
- 6–10 Start to think about ways to simplify the routine.
- 10+ Break part of the routine into a second routine and call it from the first
  routine

Moving part of a routine into another routine doesn’t reduce the overall complexity of
the program; it just moves the decision points around. But it reduces the amount of
complexity you have to deal with at any one time.