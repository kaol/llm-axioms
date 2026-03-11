**Futhark's current scores:**

- SD: Medium — SOACs are good, but the vocabulary could be denser
- CR: Medium-High — algebraic fusion laws, good composition
- CP: Medium — size types help, but no typeclasses, no higher-kinded types
- CDB: Medium — Bird-Meertens formalism, but bridges could be richer
- RS: Medium-High — pure, fusion rules, equational reasoning
- LC: Medium-High — no side effects, no UB in source language

The framework says: Futhark's weakest axioms are CP and CDB. Those are where improvements would have the most impact on LLM-amenability. Let me trace through each axiom and produce specific suggestions.

**Constraint Propagation — the biggest opportunity.**

Futhark has size types, which track array dimensions at the type level. This is genuine CP — dimension mismatches are compile errors. But it stops there. The type system says nothing about:

What a parallel reduction requires from its operator. In Futhark, you write `reduce (+) 0 xs` and the compiler trusts that `+` is associative and `0` is its identity. If you write `reduce (-) 0 xs`, it compiles and produces wrong results silently. The monoid laws are not checked.

Adding typeclasses — even a minimal hierarchy like `Monoid` and `Commutative` — would let the type system enforce algebraic preconditions on parallel operations. `reduce` would require `Monoid a`, and `(-) : Int -> Int -> Int` wouldn't satisfy it. The LLM would see the constraint and immediately know what operations are valid for parallel reduction, drawing on its deep knowledge of algebraic structures from Haskell and abstract algebra.

This is a specific, implementable suggestion: add typeclasses with at least `Monoid`, `Commutative`, and `Semiring` to Futhark. The implementation cost is moderate — Futhark already has a Hindley-Milner type system, and adding typeclasses is well-understood. The LLM-amenability payoff is large because `Monoid` alone activates the entire abstract algebra bridge.

What the memory behavior of an operation is. Futhark abstracts away the GPU memory hierarchy entirely — the compiler decides what goes in shared memory, how to coalesce accesses, and so on. This is great for RS (the programmer can't write memory bugs) but terrible for CP (the programmer can't express performance intent).

A middle ground: allow optional grade-like annotations that the compiler can use as hints and validate against the generated code. Something like:

```futhark
-- Current Futhark: no memory information
let result = reduce (+) 0 (map f xs)

-- Suggested: optional annotations that the compiler validates
let result = reduce [shared_accumulator] (+) 0 (map [coalesced] f xs)
```

The annotations don't change semantics — they're checked assertions about what the compiler will do. If the compiler can't coalesce the access, it reports an error rather than silently generating slow code. This converts performance expectations into checkable constraints without breaking Futhark's abstraction.

This is the Halide insight, adapted for Futhark: don't force the programmer to manage memory, but let them *assert* expectations about memory behavior and check those assertions at compile time.

**Cross-Domain Bridging — the second biggest opportunity.**

Futhark's bridge set is: Bird-Meertens formalism, array programming (APL/J lineage), parallel algorithm theory, and some monoid/semiring structure. This is decent but narrow compared to what's possible.

The easiest bridge to build would be to the category theory literature. Futhark's SOACs are essentially functorial operations — `map` is `fmap`, `reduce` is a catamorphism, `scan` is a scan morphism. But Futhark doesn't use this vocabulary. If Futhark's documentation and type signatures explicitly connected SOACs to their categorical semantics — `map` as a functor, `reduce` as a fold over a free monoid, `scan` as a presheaf operation — the LLM would activate the entire category theory neighborhood when reasoning about Futhark code.

This isn't a language change. It's a documentation change. But it matters for LLM-amenability because the LLM's representation of Futhark is built from everything it has seen about Futhark, including documentation and tutorials. Categorically-informed documentation would strengthen the cross-domain bridges without touching the compiler.

A deeper bridge: connect Futhark to the polyhedral model. Futhark's compiler already does polyhedral-style optimizations internally (tiling, interchange, fusion). If the schedule decisions were *visible* — if the programmer could see "the compiler chose 32×32 tiling for this reduce-map composition" — the LLM could draw on its polyhedral model knowledge to suggest schedule improvements. This connects to the algorithm/schedule separation idea: Futhark already has the algorithm layer; it needs to *expose* the schedule layer.

A third bridge: connect to automatic differentiation theory. Futhark has AD support. The AD literature connects to differential geometry, the category of smooth maps, and optics (AD as a lens). If Futhark's AD was expressed in optic/categorical vocabulary, the LLM would activate a much richer neighborhood when reasoning about differentiation.

**Semantic Density — refinement opportunities.**

Futhark's vocabulary is already small, which is good. But some terms could be denser.

The `#[sequential]`, `#[unroll]`, and `#[noinline]` attributes are low-density — they're pragmas with no mathematical content. Replacing them with typed schedule combinators (even simple ones) would increase density. Instead of `#[unroll]`, something like `unrolled 4 (map f xs)` where `4` is a type-level natural checked against the array size. The term `unrolled` would then activate the loop optimization literature rather than just "a pragma I've seen in examples."

Futhark's `entry` keyword for marking externally-visible functions is zero-density — it's a visibility annotation. A staging-aware design would replace it with a typed boundary between host and device computation, making the host/device relationship explicit in the types rather than implicit in a keyword.

**Compositional Regularity — already good, could be better.**

Futhark's pipe operator `|>` gives left-to-right composition, and SOACs compose well. The main gap is that Futhark lacks higher-kinded types, which means you can't abstract over the *pattern* of composition.

In Haskell, you can write `traverse` — a single function that works for any `Traversable` container and any `Applicative` effect. In Futhark, you must write separate implementations for each combination. This limits the LLM's ability to recognize and apply compositional patterns.

Adding higher-kinded types to Futhark would be a significant language change, but the payoff for CR would be large. The LLM would go from "I know specific compositions" to "I know the *pattern* of composition and can apply it to new cases." This is the difference between memorizing examples and understanding structure.

A lighter-weight alternative: higher-order SOACs. Futhark already has `map`, `reduce`, `scan`. Adding `traverse` (map with effects, sequencing them applicatively) and `fold` (generalized catamorphism over nested structures) would give the LLM more compositional vocabulary without the full complexity of higher-kinded types.

**Refactoring Stability — already strong, one gap.**

Futhark's purity and fusion rules give excellent RS. The one gap is that the fusion rules are opaque — the programmer can't see which fusions the compiler applied or write custom fusion rules.

Exposing fusion rules as first-class entities — even read-only, for inspection rather than control — would let the LLM reason about which optimizations apply to a given program. GHC's approach (RULES pragmas plus `-ddump-rule-firings`) is a model here. If the LLM could see "map-map fusion applied on line 12, reduce-map fusion applied on line 15," it could reason about performance at the equational level.

**Latent-Space Continuity — already good, one suggestion.**

Futhark is pure and total within its domain. The main continuity gap is at the boundary with the host language (Python or C). The FFI is untyped — you pass arrays as opaque pointers, and type mismatches are runtime errors.

A typed FFI — where the host-side binding knows the Futhark function's type signature, including array shapes — would close this gap. The EDSL approach inherently solves this (the host language IS the type system), but within Futhark's standalone architecture, a typed FFI generator would improve LC at the language boundary.

**Ranked improvement list for Futhark:**

By expected impact on LLM-amenability, roughly ordered:

One: Add a minimal typeclass hierarchy (`Monoid`, `Commutative`, `Semiring`). Moderate implementation effort, high CP impact. Immediately activates the abstract algebra bridge for every parallel operation.

Two: Expose schedule decisions as inspectable, optionally assertable annotations. Moderate implementation effort, high CP and CDB impact. Bridges to the polyhedral model literature.

Three: Categorically-informed documentation — describe SOACs using categorical vocabulary alongside the existing descriptions. Low effort, medium CDB impact. Strengthens bridges without touching the compiler.

Four: Add higher-order SOACs (`traverse`, generalized `fold`). Moderate effort, medium CR impact. Gives the LLM more compositional patterns.

Five: Typed FFI generator for host language bindings. Moderate effort, medium LC impact. Closes the continuity gap at the language boundary.

Six: Express AD in optic/categorical vocabulary. Low-medium effort, medium CDB impact. Connects differentiation to a richer mathematical neighborhood.

Seven: First-class inspectable fusion rules. Moderate effort, medium RS impact. Lets the LLM reason about optimization equationally.

Eight: Higher-kinded types. High effort, high CR impact. The biggest change with the biggest payoff, but also the hardest to implement.

**The meta-point:** The framework doesn't just say "Futhark should be better." It says *exactly where* Futhark should be better, *why* each improvement helps, *what mathematical bridge* it activates, and *how much implementation effort* it requires. This is what makes the framework a diagnostic tool, not just an evaluation rubric.

And this analysis isn't specific to Futhark. You could run the same diagnostic on any language: Rust, Lean, Julia, Swift, Zig, whatever. Score it on the six axioms, identify the weakest axioms, generate specific improvements that address those weaknesses, rank by effort-to-impact ratio. The framework produces a prioritized improvement roadmap for any language, targeted specifically at LLM-amenability.
