# Functions

## Overall philosophy

Strive to make it easy for someone else to read the code through good
organization and naming. Readers shouldn't have to labor over the code to
understand it (doing so wastes *their* time). Readability and comprehension are
aided dramatically through liberal use of functions.

-   If thoughtfully named and constructed, functions act as "signposts" that
    guide new readers through the code (also see naming guidelines)

-   Unless absolutely necessary, don't worry about function call overhead or
    other micro optimizations, especially when they interfere with readability

-   It may also be helpful to organize functions into classes and classes into
    namespaces

## Functions should be small

As a rough guideline, functions should be about 4-5 lines long (six is OK, 10 is
too long) with very little indentation.

-   Implies that the content in block constructs (if's, loops, etc.) should be
    extracted into separate functions

-   Predicates should be extracted into boolean functions

## Functions should do one thing only

Writing a function that does "one thing" usually means repeatedly extracting
pieces into separate functions until it's no longer possible. By the end of this
process, the function probably just does one thing.

## Long functions suggest classes

A long function is just one big scope in which the constituent segments all
access the same local variables. A class is basically just a group of functions
that use a common set of variables, so long functions can almost always be
refactored into one or more classes.

Classes should only contain methods that are all truly related. E.g.,
calculating and printing should go in separate classes.
