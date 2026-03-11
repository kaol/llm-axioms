# The Axioms of LLM-Amenable Design

## What This Is

A framework for evaluating how effectively a large language model can reason about, generate, and transform code in a given programming language, library, or system. The framework provides ten named, assessable dimensions that predict LLM effectiveness — not from training data volume or benchmark scores, but from structural properties of the design itself.

The core thesis: **an LLM's effectiveness is a function of how densely a language's constructs connect to structured knowledge in the latent space, how sharply the type system prunes the space of valid programs, and how well the mechanical properties of the language align with the transformer architecture.**

The framework treats the LLM as a constraint solver operating over its latent space. A programming model is LLM-amenable to the degree that its constructs activate rich knowledge neighborhoods, its types propagate constraints that eliminate invalid programs, and its syntax aligns with the physical mechanics of token prediction.

## The Ten Axioms

The axioms decompose into two layers:

**Semantic axioms (1–6)** characterize the mathematical and type-theoretic structure of the language. These determine what the LLM *can reason about*.

**Mechanical axioms (7–10)** characterize the fit between the language and the transformer architecture. These determine how efficiently the LLM *physically processes* the language.

The mechanical axioms act as friction coefficients on the semantic axioms. A language can have perfect mathematical structure, but if the tokens are fragmented, the context is distant, the syntax is ambiguous, or the training data is temporally conflicted, the effective benefit is reduced.

---

### Axiom 1: Semantic Density (SD)

*Individual constructs should activate large, structured neighborhoods in the LLM's latent space.*

A term has high semantic density when it connects to a rich, interlinked web of concepts — not just usage patterns but mathematical theory, laws, and cross-domain applications. `Monad` has high semantic density: it activates Kleisli composition, the monad laws, do-notation, free monads, the connection to monoids in endofunctor categories, adjunctions, and a vast body of worked examples across multiple domains. `cudaStream_t` has low semantic density: it activates CUDA stream API patterns and little else.

**Key property:** SD is subadditive in vocabulary size. For a fixed body of theory, adding more terms dilutes density — each term activates a smaller share of the total knowledge. This penalizes languages with large, flat APIs over languages with small, deep vocabularies.

**Key interaction:** SD and Constraint Propagation (Axiom 3) interact multiplicatively, not additively. A construct that is both semantically dense and highly constraining is exponentially more useful than one that is merely dense or merely constraining. A type signature like `traverse :: (Applicative f, Traversable t) => (a -> f b) -> t a -> f (t b)` simultaneously activates a large body of theory (SD) and eliminates nearly all implementations except the canonical one (CP). The LLM doesn't search — it recognizes.

---

### Axiom 2: Compositional Regularity (CR)

*The model's composition structure should be governed by a small number of universal, algebraically lawful combinators.*

A programming model has high compositional regularity when there exists a small set of composition operators that satisfy equational laws — associativity, functoriality, naturality — holding universally across the language. These laws must be well-represented across multiple domains in the LLM's training data (category theory, algebra, topology), not just in programming tutorials.

**Key property:** CR enables local reasoning. High CR implies that the LLM can predict the behavior of a composed expression from the behaviors of its parts independently, without needing global context. This is compositionality in the Fregean sense. It means the LLM can generate programs incrementally, validating each step locally.

**Key property:** Categorical structure maximizes CR. If a language's composition forms a category (or enriched/higher category), then CR is maximized at that expressiveness level, because the LLM has access to the entire body of category theory as a guide to valid compositions.

---

### Axiom 3: Constraint Propagation (CP)

*Type signatures and declarations should maximally prune the space of valid programs.*

Constraint propagation measures how much a typing context reduces the LLM's synthesis entropy — how much specifying types shrinks the space of programs the LLM must search. High CP means that after reading the types, the LLM's posterior is sharply peaked around correct implementations.

CP forms a hierarchy from weakest to strongest:

| Level | System | Effect on LLM |
|-------|--------|---------------|
| 0 | Untyped (Python, Lua) | No constraint propagation; LLM relies on naming conventions |
| 1 | Simply typed (Go, C) | Eliminates gross type errors; large valid space remains |
| 2 | Parametric polymorphism (ML, Java generics) | Theorems for free; polymorphic types severely constrain implementations |
| 3 | Typeclasses / traits (Haskell, Rust) | Laws and canonical instances guide toward standard implementations |
| 4 | GADTs + type families (Haskell extensions) | Computation at the type level; types encode invariants |
| 5 | Graded / quantitative types (Granule, QTT) | Types encode resource usage, effects, and coeffects |
| 6 | Full dependent types (Lean, Agda, Idris) | Types are propositions; the program is a proof; synthesis entropy approaches zero |

**Key property:** Parametricity provides free constraint propagation. Wadler's "theorems for free" means parametrically polymorphic types propagate constraints without explicit annotation. The type `forall a. [a] -> [a]` already implies the function must be a permutation/selection — the LLM gets this for free from the type system.

---

### Axiom 4: Cross-Domain Bridging (CDB)

*The language's theoretical substrate should connect to large bodies of knowledge outside programming.*

The LLM's training data is not partitioned into "programming" and "non-programming." The latent representations are shared. When a Haskell programmer writes `Applicative`, the LLM's representation is influenced by Haskell library code, category theory texts, blog posts connecting applicative functors to parser combinators, and papers on monoidal categories. All of these contribute to the LLM's ability to reason about `Applicative` in context. The Haskell code alone would be insufficient — it is the bridge to mathematics that gives the representation its depth.

**Key property:** Bridge strength is proportional to theoretical depth, not data volume. A domain contributes to bridge strength in proportion to its internal coherence and inferential density, not merely its representation in the training corpus. Category theory is a smaller corpus than JavaScript tutorials, but each page of category theory is inferentially denser.

**The Haskell anomaly explained:** LLMs are better at Haskell than training data volume would predict. This is fully explained by CDB. The LLM's Haskell competence is proportional not to Haskell code alone, but to Haskell code + category theory + type theory + abstract algebra + logic + domain theory. The cross-domain contributions dominate.

---

### Axiom 5: Refactoring Stability (RS)

*Meaning-preserving transformations should be locally characterizable and algebraically expressible.*

A programming model has high refactoring stability when there exists a rich set of equational laws such that applying a law to a subexpression preserves semantics, the applicability can be checked locally, and the laws are well-known in the LLM's training data.

**Key property:** RS requires referential transparency — or a disciplined substitute. Pure functional languages maximize RS. Imperative languages minimize it. For hardware-aware programming, where side effects are unavoidable, the question becomes: can effects be disciplined (via monads, effect systems, linear types) enough to recover local equational reasoning?

**Key property:** CR and RS are mutually reinforcing. Algebraic composition laws (CR) directly provide refactoring rules (RS). Associativity of composition gives the LLM license to re-bracket. Naturality of polymorphic functions gives the LLM license to commute maps past transformations. The richer the categorical structure, the richer the available refactorings.

---

### Axiom 6: Latent-Space Continuity (LC)

*Small perturbations in program text should correspond to small perturbations in semantics.*

A programming model has high LC when the mapping from syntax to semantics is smooth with respect to the LLM's internal metric — when nearby programs in latent space have nearby meanings. Languages with pervasive undefined behavior (C), implicit type coercions (JavaScript), or context-sensitive semantics (Perl) have low LC. Languages where every well-typed program is meaningful and nearby programs have nearby meanings have high LC.

**Key property:** LC and CP are complementary. CP shrinks the region the LLM must search; LC ensures that within the remaining region, the LLM's continuous navigation is semantically meaningful. Together they define a "safe search space" that is both small and smooth.

**What LC captures:** The absence of semantic traps — regions of the well-typed program space where code has undefined, surprising, or catastrophically discontinuous behavior. C's undefined behavior, SQL's NULL three-valued logic, JavaScript's implicit coercions. Totality contributes to LC but is not identical to it: a total language with confusing overloading can still have low LC.

---

### Axiom 7: Contextual Locality (CL)

*The information required to correctly generate a token should be syntactically close to that token.*

LLMs use self-attention mechanisms where computational cost and noise increase with sequence length. A language has high contextual locality if the semantic dependencies of any sub-expression are locally scoped — the types, declarations, and context needed to understand and generate a code fragment are syntactically nearby.

**Key property:** Global mutable state maximizes attentional distance. Languages relying on global variables, deep class hierarchies (where method resolution requires traversing distant superclasses), or implicit dependency injection force the LLM to search its entire context window to resolve references. Irrelevant tokens dilute the probability mass of the correct prediction.

**Key property:** Explicit arguments and local type signatures maximize CL. In a purely functional style with explicit parameters and local type annotations, all required context is within the immediate function body. The LLM's attention can focus narrowly and accurately.

**Relationship to CR:** Compositional Regularity (Axiom 2) is the *semantic* dimension of locality — can you reason about parts independently? CL is the *physical* dimension — is the relevant information within the attention window? A language can have perfect CR (algebraically lawful composition) but poor CL (the laws are defined in a typeclass instance in a different module). Both matter.

---

### Axiom 8: Representational Singularity (RSing)

*For semantically distinct operations, there should exist exactly one canonical syntactic representation.*

When a language provides multiple ways to express the same logic, the LLM's probability mass is fractured across valid paths. The model might begin using one idiom and continue with another, producing syntactically valid but semantically broken chimeras.

**Critical distinction:** Representational singularity applies to *semantically divergent* constructs, not to *semantically equivalent* aliases. Multiple names for the same operation (`fmap` and `<$>` in Haskell) are harmless — the LLM knows they're equivalent, and either leads to correct code. Multiple *paradigms* for the same task (React class components vs hooks vs server components) are harmful — mixing them produces bugs, and the LLM's training data contains all paradigms superimposed.

**Key property:** Low syntactic variance for divergent constructs focuses probability mass. Go (one loop construct, one error handling pattern) forces the LLM's distribution to peak sharply around the canonical choice. C++ (for, while, do-while, std::for_each, ranges, goto) diffuses confidence across alternatives with different edge cases.

---

### Axiom 9: Token-Semantic Alignment (TSA)

*The lexical units of the language should align with the subword token boundaries of the LLM's tokenizer.*

LLMs read tokens, not characters. A construct has poor token-semantic alignment if a single semantic concept requires multiple meaningless subword tokens. Dense symbolic operators (`<*>`, `>>=`, `⍋`) are often shattered by tokenizers trained primarily on natural language, destroying the semantic unit at the mechanical level.

**Key property:** Word-based identifiers align with natural language tokenization. `traverse`, `reduce`, `Monoid` are each likely to be single tokens that trigger the correct latent neighborhood. `<*>` is likely three tokens (`<`, `*`, `>`) that individually carry no relevant meaning.

**Practical consequence:** This axiom is a modifier on Semantic Density (Axiom 1). A term with high mathematical SD but poor token alignment has *reduced effective SD* — the rich neighborhood exists in the latent space but the tokenized input doesn't reliably reach it. Languages where SD comes from word-like terms (Haskell's typeclass names, MLIR's operation names) are more robust than languages where SD comes from symbolic operators (APL, heavily point-free Haskell).

**Caveat:** TSA is partially contingent on the specific tokenizer, which varies across models. It is more of an engineering concern than a language-intrinsic property. But in practice, most current LLMs use tokenizers with similar biases (trained on English-heavy web corpora), making TSA a reliable heuristic.

---

### Axiom 10: Chronological Invariance (CI)

*The semantics of the language's core abstractions should remain stable across the timespan of the LLM's training data.*

LLM training data is a compressed snapshot spanning years of code repositories. If a language undergoes paradigm-shifting changes, its latent space becomes temporally smeared — the LLM's representation is a superposition of incompatible epochs, and it will generate chimeras mixing old and new patterns.

**Key property:** High semantic drift causes temporal hallucination. React (class components → hooks → server components) is the canonical example. The LLM's training data contains massive volumes of all three paradigms. The latent space is chronologically conflicted, and the LLM frequently generates code mixing patterns from incompatible eras.

**Key property:** Mathematical stability provides maximal CI. Category theory hasn't changed since 1945. The monad laws haven't changed since Moggi's 1991 paper. Haskell's `Functor`/`Applicative`/`Monad` hierarchy has been stable for over a decade. This means 100% of the mathematical training data points toward the same semantic truth, maximizing predictive reliability. Languages grounded in stable mathematics inherit that stability.

**Implication for new languages:** A new language with zero training data but deep mathematical foundations gets CI for free — the mathematical literature it bridges to is temporally stable. The cross-domain bridges (Axiom 4) point to eternal truths rather than evolving APIs. This is a structural advantage of the "design for mathematics" approach.

---

## The Axiom Interaction Model

The ten axioms are not independent. They form a structured interaction:

**The semantic core (Axioms 1–6)** determines the theoretical ceiling — the best the LLM could do with infinite context, perfect tokenization, and stable training data.

**The mechanical layer (Axioms 7–10)** determines what fraction of that ceiling is realized in practice, given the physical constraints of the transformer.

The effective LLM-amenability is:

```
Effective = Semantic(SD, CR, CP, CDB, RS, LC) × Mechanical(CL, RSing, TSA, CI)
```

where the semantic component is the geometric mean of axioms 1–6 (multiplicative because one catastrophic weakness dominates) and the mechanical component is a friction coefficient between 0 and 1 that discounts the semantic score based on attention limitations, probability diffusion, tokenization friction, and temporal smearing.

**Key interactions across layers:**

- **SD × TSA:** Semantic density is discounted by poor token alignment. A term with rich mathematical meaning but fragmented tokenization has reduced effective SD.
- **CP × CL:** Constraint propagation is discounted by poor contextual locality. A type constraint defined far from its use site provides less guidance to the LLM during generation.
- **CDB × CI:** Cross-domain bridging is amplified by chronological invariance. Bridges to stable mathematical theory are more reliable than bridges to evolving frameworks.
- **CR × RSing:** Compositional regularity is discounted by representational variance. If composition can be expressed in multiple divergent syntactic forms, the LLM's confidence in each form is reduced.

---

## The Convergence Hypothesis

**Conjecture:** Optimizing a programming model for LLM-amenability — maximizing all ten axioms subject to faithfulness to the computational target — recovers designs that are independently well-motivated by mathematical and engineering principles. The design that serves the LLM also serves the human.

**Argument:** The semantic axioms independently select for mathematical structure: SD for deep theory, CR for categorical composition, CP for expressive types, CDB for mathematical foundations, RS for equational theories, LC for well-defined semantics. The mechanical axioms select for clarity and stability: CL for explicitness, RSing for canonicality, TSA for readable names, CI for timeless foundations. The conjunction of all ten selects for mathematically-grounded designs with clean, explicit, stable interfaces — which is what good engineering has always aimed for.

**Corollary (Aesthetic Convergence):** A language optimized for LLM-amenability will "feel right" to a mathematically-minded programmer, because both the LLM's navigability and the programmer's sense of elegance are responding to the same underlying signal: the density and coherence of mathematical structure.

---

## Applying the Framework

The axioms work as a diagnostic tool for any computational system:

**For programming languages:** Score each axiom, identify the weakest, generate specific improvements ranked by effort and impact.

**For libraries and APIs:** Score the public interface, identify where semantic density can be increased (e.g., adding a Profunctor instance), where contextual locality can be improved (e.g., keeping typeclass instances near use sites), where representational singularity can be enforced (e.g., deprecating redundant API paths).

**For frameworks:** Identify which mathematical structures are hidden behind imperative APIs (signal/slot → FRP, model/view → optics, state machines → automata theory) and reveal them through typed interfaces.

**For hardware architectures:** Evaluate the ISA's computational model on the axioms. Spatial/dataflow architectures score higher than von Neumann because their composition is topological (high CR), their types enforce connectivity (high CP), and their theoretical substrate is monoidal categories (high CDB).

**For build systems, databases, protocols, configuration languages:** The same ten axioms apply. Every system has mathematical structure — dependency graphs, relational algebra, session types, configuration lattices. The axioms ask: is that structure visible in the types, connected to theory, and stable across time?

---

## Predictions

The framework generates specific, falsifiable predictions:

1. LLMs generate correct Futhark code at higher rates than correct CUDA code for equivalent tasks, despite more CUDA training data. (Tests CDB: mathematical structure compensates for smaller corpus.)

2. Adding type signatures improves LLM correctness more in Haskell than in Python. (Tests CP: rich types propagate more constraints.)

3. LLM-generated cmake has higher error rates than LLM-generated Nix for equivalent build tasks. (Tests the full framework: Nix scores higher on SD, CR, CP, RS, LC, CI.)

4. Renaming identifiers from operational names (`decode_unit`) to mathematical names (`StackEffectHomomorphism`) measurably improves LLM assistance quality for the same code. (Tests SD + TSA: word-based mathematical names activate denser neighborhoods.)

5. LLM-generated React code has higher rates of paradigm-mixing bugs than LLM-generated Elm code. (Tests CI: React's temporal smearing vs Elm's stable architecture.)

6. A language designed to maximize all ten axioms enables LLM code generation competitive with established languages despite having zero training data, bootstrapping competence from mathematical adjacency alone. (Tests the entire framework.)

---

## Summary

| # | Axiom | Layer | Measures | Maximized By |
|---|-------|-------|----------|-------------|
| 1 | Semantic Density | Semantic | Knowledge activated per construct | Small vocabulary over deep theory |
| 2 | Compositional Regularity | Semantic | Lawfulness of composition | Categorical structure |
| 3 | Constraint Propagation | Semantic | Pruning power of types | Dependent/graded/linear type systems |
| 4 | Cross-Domain Bridging | Semantic | Connections beyond programming | Mathematical foundations |
| 5 | Refactoring Stability | Semantic | Soundness of local rewrites | Equational theories, purity |
| 6 | Latent-Space Continuity | Semantic | Smoothness of syntax→semantics | Totality, no undefined behavior |
| 7 | Contextual Locality | Mechanical | Attentional distance to context | Pure functions, local types |
| 8 | Representational Singularity | Mechanical | Canonicality of expression | One way per semantic intent |
| 9 | Token-Semantic Alignment | Mechanical | Tokenizer compatibility | Word-based identifiers |
| 10 | Chronological Invariance | Mechanical | Temporal stability of semantics | Stable standards, mathematical foundations |
