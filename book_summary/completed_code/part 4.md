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
- The number of lines of code in which the objects are “live” is small

## Using Conditionals

### 15.1 if Statements

- Write the nominal path through the code first; then write the unusual cases
- Put the normal case after the if rather than after the else
- Simplify complicated tests with boolean function calls
- In case Use the default clause to detect errors

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
- Use break and continue only with caution Use of break eliminates the possibility of
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

19.2 Compound Statements
