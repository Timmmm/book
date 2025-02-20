Evaluate Expressions Only
========================

{{#include ../links.md}}

Very often, a use case does not require a full-blown scripting _language_, but only needs to
evaluate _expressions_.

In these cases, use the `Engine::compile_expression` and `Engine::eval_expression` methods or their
`_with_scope` variants.

```rust,no_run
let result = engine.eval_expression::<i64>("2 + (10 + 10) * 2")?;
```

When evaluating _expressions_, no full-blown statement (e.g. [`if`], [`while`], [`for`], `fn`)
&ndash; not even [variable] assignment &ndash; is supported and will be considered parse errors.

```rust,no_run
// The following are all syntax errors because the script is not an expression.

engine.eval_expression::<()>("x = 42")?;

let ast = engine.compile_expression("let x = 42")?;

let result = engine.eval_expression_with_scope::<i64>(&mut scope, "if x { 42 } else { 123 }")?;
```
