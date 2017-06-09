# Function structure

## Function signatures should be small

Three arguments max. Anything more than this begs refactoring.

A function with a lot of arguments might be turned into a class with a simple
constructor accepting just the most important arguments and setters for the
others. (Passing in a map-like container of options is probably fine if needed,
but using setters is cleaner.)

## Good and bad types for function arguments

Avoid boolean args. A boolean arg suggests that the function does one of two
things. The better approach is to write two functions: one for the true case and
one for the false case.

Similarly, avoid null args, which imply a null case and a not-null case.

*Don't use output arguments*. Arguments should only be used for data going
*into* a function, not as placeholders for data coming out.

## Organization and layout of classes

**Stepdown rule:** Put important stuff at the top and put details at the bottom
(like a newspaper/magazine article). Ideally, there should be no back
references, i.e., a function shouldn't call any functions above it.

## Eliminating switch and if statements

Defensive programming, i.e., constantly checking inputs, is a code smell. It's
better to write good tests and to trust them. However, coding defensively *is*
good practice for public APIs.

Consider eliminating switch and if statements by taking advantage of
polymorphism/polymorphic dispatch. For example, turn this:

```python
# a.py -------------------------------------------
class StringArg(str):
    def to_int(self):
        return int(self)

class IntegerArg(int):
    pass


# b.py -------------------------------------------
import a

def add1(arg):
    if isinstance(arg, a.StringArg):
        return arg.to_int() + 1
    elif isinstance(arg, a.IntegerArg):
        return arg + 1


# main.py ----------------------------------------
import a
import b

b.add1(a.IntegerArg(1))  # 2
b.add1(a.StringArg(1))  # 2
```

into this:

```python
# a.py -------------------------------------------
import b

class StringArg(b.Arg, str):
    def to_int(self):
        return int(self)

    def add(self, other):
        return self.to_int() + other

class IntegerArg(b.Arg, int):
    def add(self, other):
        return self + other


# b.py -------------------------------------------
class Arg(object):
    def add(self, other):
        raise NotImplementedError

def add1(arg):
    return arg.add(1)


# main.py ----------------------------------------
import a
import b

b.add1(a.IntegerArg(1))  # 2
b.add1(a.StringArg(1))  # 2
```

Obviously this is a silly, unrealistic example, but it presents a design
alternative to type checking arguments.

In addition to making the code cleaner, this strategy comes with a significant
design advantage known as **dependency inversion**.

-   In its first incarnation, the example above is not independently
    deployable/developable. That is, changes in module A could affect module B
    (this is certain to occur for compiled languages). A characteristic
    attribute of this design is that the source code dependencies (module B
    imports module A) are aligned with the flow of control (`add1` in module B
    processes instances of `StringArg` or `IntegerArg`, which are defined in
    module A).

-   The rewritten example is better because the business rule in module B has no
    source code depence on the implementation details in module A. In other
    words, changes in module A won't affect module B. (Note that I've made
    module A to depend on module B. This is acceptable because changing a
    business rule could undoubtedly require changes in the implementation
    details.) This is accomplished by stipulating that modules A and B both
    agree (or are forced) to use a common interface, effectively moving the
    source code dependence out of alignment with the flow of control.

Note that switch/if statements are OK if the cases are all self-contained in the
module because there won't be any dependency issues.

**Note:** It's not mentioned in the video, but these same ideas are described in
"Clean Architecture."

## Code partitioning

In a module diagram, there should be a clear line separating core application
functionality -- the "application partition" -- and the low-level details
(factories, config data, etc.) -- the "main" partition.

Note that the main partition should depend on the application, not the other way
around! Main is effectively a plugin to the application (this is technique
called "dependency injection"), and the application shouldn't know anything
about main.

Dependency inversion can help with this. For example, the rewritten example in
the last section shows that the implementation details in module A could be
merged into the main module if desired. This was not possible in the original
version. Modules A and main make up the "main partition" of the code, *nothing*
in which is a dependency of the "application partition" consisting of module B.

The application partition is usually large and can be further partitioned if
needed. The goal is to have independently deployable/developable modules.

## Eliminating/constraining assignments and side effects

The core ideas of functional programming, e.g., immutability, purity, and being
mindful about state, can be applied to object-oriented programming for better,
safer code.

Watch our for temporal coupling (e.g., open and close, set and reset, etc.).

One strategy for dealing with temporal coupling is **passing a block**: make a
function that takes a "block" (a function) and executes it between temporally
coupled parts. E.g., `function f(g) {open(); g(); close();}`. (This is like
using a context manager in Python.)

To help eliminate surprise side effects, use the principle of **command-query
separation**:

-   Functions that return values (queries) should not change state
-   Functions that change state (commands) should not return values, including
    error codes. (Instead of returning an error code, raise an exception.)

## Tell, don't ask

Tell another object what to do, don't ask what its state is. Doing the former
implies that the asker will take responsibility for the object and use the
response to make an action on the object's behalf. It's better to let the
object, which already knows its own state, be responsible for itself.

The tell-don't-ask principle implies that when an object is told to return
something, the object can do its own error handling. This allows for good error
handling practices while maintaining clarity and cleanliness.

Using tell-don't-ask reduces the need for query functions, and therefore helps
enforce the **Law of Demeter**. This law states that a single function should
not have knowledge of the system's overall structure. Otherwise the function is
too coupled to the system and becomes brittle to changes. Instead, depend on
neighboring objects to get things done and let instructions propagate through
the system.

Avoid "trainwrecks" like `obj.getX().getY().getZ().doSomething()`, a clear
violation of both tell-don't-ask and the Law of Demeter (asking several times
before telling, knowing too much about the system's structure). Rewrite a
trainwreck like `obj.doSomething()` which tells x to do something, which tells y
to do something, etc. The call will eventually reach its final destination.

The Law of Demeter formalizes tell-don't-ask:

-   A method of an object may be called if the object is,

    -   Passed as an argument
    -   Created locally
    -   An instance variable
    -   Global

-   A method of an object may not be called if the object is,

    -   Returned from a previous method call (doing so results in trainwreck
        chains)

**Note:** Long method chains shouldn't themselves be judged as a code smell. For
example, the Pandas package for Python encourages method chains for building
elegant transformation pipelines of DataFrames, where each method call returns a
new DataFrame that can be operated on by another method call. In this case there
is no violation of the Law of Demeter or of tell-don't-ask.

"...any function that *tells* instead of *asks* is decoupled from its
surroundings."

## Structured programming

In structured programming paradigm, all algorithms are composed of three basic
operations:

-   Sequence: one block connects to another, `block -> block`
-   Selection: choice between two blocks (like the ternary operator), `{condition} ? block : block`
-   Iteration: repeatedly execute a block under some condition, `while {condition}: block`

