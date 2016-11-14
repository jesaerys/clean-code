# Names

Names should communicate. They are not for the convenience of the *writer* of
the code, but rather for the *reader* of the code. Ideally, names should be
assigned such that statements form sentences that read like prose, because code
should be written for human consumption.

## Reveal intent

Names should reveal intent of the code without resorting to comments. If a
variable needs a comment after its definition, then it needs a better name.

## Describe the problem

The meaning of a name should be clear without having to read other code.

## Avoid disinformation

A name should be consistent with what its thing actually does. This is mostly
relevant to when code is changed and refactored; names should be accurate after
any changes. Also, a name shouldn't be too specific or too abstract.

## Be pronounceable

Pronounceability begets readability.

## Avoid encodings

Encodings -- such as the Hungarian prefix convention -- tend to hinder
pronounceability and readability. Furthermore, encoding schemes aren't really
even necessary because IDEs/editors can be used to reveal types and compilers
and unit tests will protect against type errors.

## Use parts of speech

Use nouns, verbs, etc. so that statements read like sentences in prose. Below
are some guidelines for achieving this.

*(Note: This section has some bias toward classes and OOP, but the overal spirit
is applicable to other programming paradigms.)*

1.  Use nouns or noun phrases for classes and variables, e.g.,
    -    account
    -    message_parser

    Variable (instance) names should ideally related to the classes they point
    to (e.g., the variable "account" might hold an instance of the "Account"
    class).

    Avoid vague "noise" words like,
    -    manager
    -    processor
    -    data
    -    info

2.  Use predicates for boolean variables and methods that return booleans. These
    read well in if statements. E.g., "isEmpty" or "isEmpty()".

3.  Use verbs or verb phrases for methods, which read will in imperative
    statements. E.g.,
    -   postPayment
    -   getPrice

4.  Use a prefix such as "get" for accessors. Don't use nouns. E.g.,
    -   getName
    -   getFuelRate

5.  Use nouns or predicates for properties ("variables" that are actually
    methods).

6.  Use adjectives for enums (collections of states or object descriptions).
    E.g.,
    -   Color(RED, GREEN, BLUE)
    -   Status(PENDING, CLOSED, CANCELLED)
    -   Size(SMALL, MEDIUM, LARGE)

## Follow the scope length rule

Variables available to a wide/"long" scope should have long names so that their
meaning is clear when used throughout the codebase. Variables available only to
a narrow/"short" scope can afford to have (and should have) shorter names,
especially if the type declaration is nearby.

Conversely, functions and classes should follow the opposite rule: use shorter
names when the scope is wide and longer names when the scope is narrow.
Functions/classes that have a wider scope will presumably be called frequently
and shorter names make referring to them easier.

A child/derived class should have a name that is more specific than its parent,
likely by adding adjectives to the parent's name.
