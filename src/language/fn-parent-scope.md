Call a Function Within the Caller's Scope
========================================

{{#include ../links.md}}


Peeking Out of The Pure Box
---------------------------

Rhai [functions] are _pure_, meaning that they depend on on their arguments and have no access to
the calling environment.

When a [function] accesses a [variable] that is not defined within that [function]'s scope, it
raises an evaluation error.

It is possible, through a special syntax, to actually run the [function] call within the scope of
the parent caller &ndash; i.e. the scope that makes the [function] call &ndash; and access/mutate
variables defined there.

Obviously, this is only meaningful for _scripted_ [functions], since native Rust functions can never
access any scope anyway.

```rust,no_run
fn foo(y) {             // function accesses 'x' and 'y', but 'x' is not defined
    x += y;             // 'x' is modified in this function
    let z = 0;          // 'z' is defined in this function's scope
    x
}

let x = 1;              // 'x' is defined here in the parent scope

foo(41);                // error: variable 'x' not found

// Calling a function with a '!' causes it to run within the caller's scope

foo!(41) == 42;         // the function can access and mutate the value of 'x'!

x == 42;                // 'x' is changed!

z == 0;                 // <- error: variable 'z' not found

x.method!();            // <- syntax error: not allowed in method-call style

// Also works for function pointers

let f = Fn("foo");

call!(f, 42) == 84;     // must use function-call style

x == 84;                // 'x' is changed once again

f.call!(41);            // <- syntax error: not allowed in method-call style

// But not allowed for module functions

import "hello" as h;

h::greet!();            // <- syntax error: not allowed in namespace-qualified calls
```


New Variables Are Not Retained
-----------------------------

[Variables] or [constants] defined within the [function] are _not_ retained.
They remain local to the [function].


The Caller's Scope Can be Mutated
--------------------------------

Changes to variables in the calling scope persist.

Therefore, with this syntax, it is possible for a Rhai [function] to mutate its calling environment.


Caveat Emptor
-------------

Functions relying on the calling scope is often a _Very Bad Idea™_ because it makes code almost
impossible to reason about and maintain, as their behaviors are volatile and unpredictable.

Rhai functions are normally _pure_, meaning that you can rely on the fact that they never mutate the
outside environment.  Using this syntax breaks this guarantee.

Functions called in this manner behave more like macros that are expanded inline than actual
function calls, thus the syntax is also similar to Rust's macro invocations.

This usage should be at the last resort. **YOU HAVE BEEN WARNED**.
