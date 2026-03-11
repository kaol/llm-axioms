# The Axioms of LLM-Amenable Design

## What This Is

A framework for evaluating how effectively a large language model can reason about, generate, and transform expressions within a formal system. The framework provides ten named, assessable dimensions that predict LLM effectiveness — not from training data volume or benchmark scores, but from structural properties of the system itself.

The framework was developed for programming languages but applies to any formal system: programming languages, libraries, hardware architectures, build systems, databases, governance systems, physical theories, and any other domain with compositional structure and typed constraints.

The core thesis: **an LLM's effectiveness with a formal system is determined by how densely the system's constructs connect to structured knowledge in the latent space, how sharply its constraints prune the space of valid expressions, and how well its mechanical properties align with the transformer architecture.**

The framework treats the LLM as a constraint solver navigating its latent space. A formal system is LLM-amenable to the degree that its constructs activate rich knowledge neighborhoods, its constraints eliminate invalid outputs, and its surface form aligns with the physical mechanics of token prediction.

---

## The Ten Axioms

The axioms decompose into two layers:

**Semantic axioms (1–6)** characterize the mathematical and structural properties of the system. These determine the theoretical ceiling — what the LLM *can reason about*.

**Mechanical axioms (7–10)** characterize the fit between the system's surface form and the transformer architecture. These determine the friction — how efficiently the LLM *physically processes* the system.

The effective amenability is the semantic ceiling discounted by mechanical friction. A system with perfect structure but catastrophic tokenization, or perfect syntax but no mathematical depth, scores poorly. Both layers are necessary.

All axiom scores are relative to a target domain: `Axiom(System, Target)`. The semantic density of a construct depends on which domain it's being applied to — a term can be dense in one domain and irrelevant in another. Scores given without a target implicitly assume the system's primary domain.

---

### Axiom 1: Semantic Density (SD)

*Individual constructs should activate large, structured neighborhoods in the LLM's latent space.*

A term has high semantic density when it connects to a rich, interlinked web of concepts — not just usage patterns but theory, laws, and cross-domain applications. `Monad` has high semantic density: it activates Kleisli composition, the monad laws, do-notation, free monads, adjunctions, and a vast body of worked examples across multiple domains. `cudaStream_t` has low semantic density: it activates CUDA stream API patterns and little else. In physics, `gauge symmetry` has extraordinary semantic density: it activates fiber bundles, representation theory, conservation laws, the Standard Model, and the entire body of modern particle physics from a two-word phrase.

**Key property:** SD is subadditive in vocabulary size. For a fixed body of theory, adding more terms dilutes density — each term activates a smaller share of the total knowledge. This penalizes systems with large, flat vocabularies over systems with small, deep ones.

**Key interaction:** SD and Constraint Propagation (Axiom 3) interact multiplicatively. A construct that is both semantically dense and highly constraining is exponentially more useful than one that is merely dense or merely constraining. A type signature like `traverse :: (Applicative f, Traversable t) => (a -> f b) -> t a -> f (t b)` simultaneously activates a large body of theory (SD) and eliminates nearly all implementations except the canonical one (CP). The LLM doesn't search — it recognizes.

---

### Axiom 2: Compositional Regularity (CR)

*The system's composition structure should be governed by a small number of universal, algebraically lawful combinators.*

A system has high compositional regularity when there exists a small set of composition operators that satisfy equational laws — associativity, functoriality, naturality — holding universally across the system. These laws must be well-represented across multiple domains in the LLM's training data, not just within the system's own documentation.

**Key property:** CR enables local reasoning. High CR implies that the LLM can predict the behavior of a composed expression from the behaviors of its parts independently, without needing global context. This is compositionality in the Fregean sense.

**Key property:** Categorical structure maximizes CR. If a system's composition forms a category (or enriched/higher category), then CR is maximized at that expressiveness level, because the LLM has access to the entire body of category theory as a guide to valid compositions.

**Cross-domain observation:** CR applies equally to programming (function composition), physics (the composition of physical theories must be algebraically coherent), governance (decision procedures must compose across levels), and any other domain with interacting parts. Systems where the parts compose cleanly score high. Systems where the parts interact through ad hoc interfaces score low.

---

### Axiom 3: Constraint Propagation (CP)

*Declarations and structural commitments should maximally prune the space of valid expressions.*

Constraint propagation measures how much a structural specification reduces the LLM's synthesis entropy — how much specifying the "types" of a system shrinks the space the LLM must search. High CP means that after reading the constraints, the LLM's posterior is sharply peaked around correct outputs.

In programming, CP manifests as type systems of increasing power:

| Level | System | Effect on LLM |
|-------|--------|---------------|
| 0 | Untyped (Python, Lua) | No constraint propagation; LLM relies on naming conventions |
| 1 | Simply typed (Go, C) | Eliminates gross type errors; large valid space remains |
| 2 | Parametric polymorphism (ML, Java generics) | Theorems for free; polymorphic types severely constrain implementations |
| 3 | Typeclasses / traits (Haskell, Rust) | Laws and canonical instances guide toward standard implementations |
| 4 | GADTs + type families (Haskell extensions) | Computation at the type level; types encode invariants |
| 5 | Graded / quantitative types (Granule, QTT) | Types encode resource usage, effects, and coeffects |
| 6 | Full dependent types (Lean, Agda, Idris) | Types are propositions; the program is a proof; synthesis entropy approaches zero |

But CP is not limited to programming. In physics, gauge symmetry is constraint propagation — specifying the gauge group determines the form of all interactions. In governance, constitutional constraints propagate through the decision space — "Congress shall make no law" eliminates whole categories of legislation. In any formal system, the question is: how much does specifying the structure constrain the valid outputs?

**Key property:** Parametricity provides free constraint propagation. Wadler's "theorems for free" means parametrically polymorphic types propagate constraints without explicit annotation. The type `forall a. [a] -> [a]` already implies the function must be a permutation/selection — the LLM gets this for free.

**Key diagnostic:** When a system has high CP for form but low CP for parameters — when the structure of interactions is constrained but their strength is not — this indicates an incomplete theory. The Standard Model of particle physics has this exact profile: gauge symmetry determines the form of every interaction but leaves 19 parameters unfixed. Every free parameter is a missing constraint.

---

### Axiom 4: Cross-Domain Bridging (CDB)

*The system's theoretical substrate should connect to large bodies of knowledge outside the system's own domain.*

The LLM's training data is not partitioned by discipline. The latent representations are shared. A term's effective meaning is shaped by all domains in which it appears. When a programmer writes `Applicative`, the LLM's representation draws on Haskell code, category theory texts, blog posts on parser combinators, and papers on monoidal categories simultaneously. The programming knowledge alone would be insufficient — it is the bridge to mathematics that gives the representation its depth.

**Key property:** Bridge strength is proportional to theoretical depth, not data volume. A domain contributes to bridge strength in proportion to its internal coherence and inferential density, not merely its representation in the training corpus. Category theory is a smaller corpus than JavaScript tutorials, but each page of category theory is inferentially denser.

**The Haskell anomaly explained:** LLMs perform better with Haskell than training data volume predicts. CDB explains this: the LLM's Haskell competence draws on Haskell code + category theory + type theory + abstract algebra + logic + domain theory. The cross-domain contributions dominate the native corpus.

**Cross-domain observation:** CDB predicts which fields will benefit most from LLM assistance. Fields with rich bridges to other structured knowledge — algebraic topology, theoretical physics, functional programming — will see larger productivity gains than fields that are technically dense but theoretically isolated.

---

### Axiom 5: Refactoring Stability (RS)

*Meaning-preserving transformations should be locally characterizable and algebraically expressible.*

A system has high refactoring stability when there exists a rich set of equational laws such that applying a law to a subexpression preserves meaning, the applicability can be checked locally, and the laws are well-known in the LLM's training data.

**Key property:** RS requires referential transparency — or a disciplined substitute. Pure functional languages maximize RS. Imperative languages minimize it. In other domains: mathematical theories with clean equational theories (algebra, category theory) have high RS. Theories where small changes in assumptions produce large changes in conclusions (some areas of PDE theory, fragile political systems) have low RS.

**Key property:** CR and RS are mutually reinforcing. Algebraic composition laws (CR) directly provide refactoring rules (RS). Associativity of composition gives the LLM license to re-bracket. Naturality gives the LLM license to commute transformations. The richer the categorical structure, the richer the available refactorings.

**Cross-domain observation:** In governance, constitutional amendment is a refactoring mechanism. In physics, gauge invariance and the renormalization group are refactoring mechanisms. The axiom applies wherever "same meaning, different form" is a meaningful concept.

---

### Axiom 6: Latent-Space Continuity (LC)

*Small perturbations in the system's expressions should correspond to small perturbations in meaning.*

A system has high LC when the mapping from form to meaning is smooth — when nearby expressions have nearby meanings. Systems with pervasive undefined behavior (C), implicit type coercions (JavaScript), or context-sensitive semantics (Perl) have low LC. Systems where every well-formed expression is meaningful and nearby expressions have nearby meanings have high LC.

**Key property:** LC captures the absence of semantic traps — regions of the well-formed expression space where outputs are undefined, surprising, or catastrophically discontinuous. Totality contributes to LC but is not identical to it: a total system with confusing overloading can still have low LC.

**Key property:** LC and CP are complementary. CP shrinks the region the LLM must search; LC ensures that within the remaining region, the LLM's continuous navigation is semantically meaningful. Together they define a "safe search space" that is both small and smooth.

**Key diagnostic:** When a system has a specific LC failure, it often indicates a structural incompleteness. In physics, the non-renormalizability of perturbative quantum gravity is an LC failure — small changes in energy scale produce infinite changes in predictions. This signals that the perturbative description is incomplete, not that gravity is discontinuous. LC failures are diagnostic: they point at where the current formalism breaks down.

---

### Axiom 7: Contextual Locality (CL)

*The information required to correctly generate an expression should be structurally close to that expression.*

LLMs use attention mechanisms where computational cost and noise increase with distance. A system has high contextual locality if the dependencies of any sub-expression are locally scoped — the types, declarations, and constraints needed to understand and generate a fragment are structurally nearby.

**Key property:** Global mutable state maximizes attentional distance. Systems relying on global variables, deep inheritance hierarchies, or implicit dependencies force the LLM to search its entire context to resolve references. Irrelevant information dilutes the probability mass of the correct prediction.

**Key property:** Explicit arguments and local annotations maximize CL. In a purely functional style with explicit parameters and local type annotations, all required context is within the immediate scope.

**Cross-system locality:** CL degrades multiplicatively across system boundaries. A hardware/software system where the hardware is described in Clash, the driver in C, and the runtime in Haskell has three CL domains. Even if each layer individually has good CL, the interfaces between layers introduce CL penalties that compound. The weakest interface dominates overall LLM assistance quality.

**Relationship to CR:** Compositional Regularity (Axiom 2) is the *semantic* dimension of locality — can you reason about parts independently? CL is the *physical* dimension — is the relevant information within the attention window? A system can have perfect CR but poor CL. Both matter.

---

### Axiom 8: Representational Singularity (RSing)

*For semantically distinct operations, there should exist a canonical form.*

When a system provides multiple ways to express the same intent, the LLM's probability mass fractures across valid paths. The model may begin using one idiom and continue with another, producing syntactically valid but semantically broken chimeras.

**Severity spectrum:** Not all representational variance is equally harmful:

| Level | Type | Example | Harm |
|-------|------|---------|------|
| 0 | Alias | `fmap` / `<$>` in Haskell | None; LLM knows equivalence |
| 1 | Stylistic | Clash mealy / signal composition | Mild; mixing is awkward but not broken |
| 2 | Generational | React class components / hooks | Moderate; mixing produces bugs |
| 3 | Paradigmatic | OOP / FP in the same language | Severe; fundamental semantic conflicts |

The axiom primarily targets Level 2 and Level 3 variance. Level 0 variance is harmless. Level 1 variance is a minor friction. The distinction matters because naively enforcing "one way to do everything" (as Go does) eliminates all levels including the harmless ones, while the real problem is specifically generational and paradigmatic divergence.

---

### Axiom 9: Token-Semantic Alignment (TSA)

*The lexical units of the system should align with the subword token boundaries of the LLM's tokenizer.*

LLMs read tokens, not characters. A construct has poor token-semantic alignment if a single semantic concept requires multiple meaningless subword tokens. Dense symbolic operators (`<*>`, `>>=`, `⍋`) are often shattered by tokenizers trained primarily on natural language, destroying the semantic unit at the mechanical level.

**Key property:** Word-based identifiers align with natural language tokenization. `traverse`, `reduce`, `Monoid`, `StackEffectHomomorphism` are each likely to be tokenized in semantically meaningful chunks that trigger correct latent neighborhoods. `<*>` is likely three tokens that individually carry no relevant meaning.

**Practical consequence:** TSA is a modifier on Semantic Density (Axiom 1). A term with high mathematical SD but poor token alignment has reduced effective SD. Systems where density comes from word-like terms are more robust than systems where density comes from symbolic operators.

**Caveat:** TSA is partially contingent on the specific tokenizer, which varies across models. It is more of an engineering concern than a system-intrinsic property. But in practice, most current LLMs use tokenizers with similar biases, making TSA a reliable heuristic.

---

### Axiom 10: Chronological Invariance (CI)

*The semantics of the system's core constructs should remain stable across the timespan of the LLM's training data.*

LLM training data is a compressed snapshot spanning years. If a system undergoes paradigm-shifting changes, its latent space becomes temporally smeared — the LLM's representation is a superposition of incompatible epochs, producing chimeras.

**Key distinction — drift vs accretion:** Semantic *drift* occurs when existing constructs change meaning (React class components becoming anti-patterns). Semantic *accretion* occurs when new constructs are added while existing ones remain stable (WASM adding GC proposal while the core spec is unchanged). Both cause temporal confusion but through different mechanisms. Drift produces chimeras (mixing old and new semantics of the same construct). Accretion produces version confusion (using constructs that don't exist in the target version). Drift is more harmful because it corrupts the LLM's representation of existing constructs; accretion only adds noise about new ones.

**Key property:** Mathematical stability provides maximal CI. Category theory hasn't changed since 1945. The monad laws haven't changed since 1991. Systems grounded in stable mathematics inherit that stability — 100% of the mathematical training data points toward the same truth.

**Implication for new systems:** A new system with zero training data but deep mathematical foundations gets CI for free — the mathematical literature it bridges to is temporally stable.

---

## The Axiom Interaction Model

The ten axioms are not independent. They form a structured interaction:

**The semantic core (Axioms 1–6)** determines the theoretical ceiling — the best the LLM could do with infinite context, perfect tokenization, and stable training data.

**The mechanical layer (Axioms 7–10)** determines what fraction of that ceiling is realized in practice, given the physical constraints of the transformer.

```
Effective = Semantic(SD, CR, CP, CDB, RS, LC) × Mechanical(CL, RSing, TSA, CI)
```

The semantic component is the geometric mean of axioms 1–6 (multiplicative because one catastrophic weakness dominates). The mechanical component is a friction coefficient between 0 and 1.

**Key interactions across layers:**

- **SD × TSA:** Semantic density is discounted by poor token alignment. Rich meaning behind fragmented tokens has reduced effective SD.
- **CP × CL:** Constraint propagation is discounted by poor contextual locality. A constraint defined far from its use site provides less guidance during generation.
- **CDB × CI:** Cross-domain bridging is amplified by chronological invariance. Bridges to stable theory are more reliable than bridges to evolving frameworks.
- **CR × RSing:** Compositional regularity is discounted by representational variance. Multiple divergent syntactic forms for the same composition reduce confidence.

**System-level composition:** When a system spans multiple subsystems (e.g., hardware in Clash, driver in C, runtime in Haskell), the overall LLM-amenability is closer to the minimum across subsystems than the mean. The weakest component dominates, and the interfaces between components compound CL penalties. Systems whose layers compose with algebraic regularity (high inter-layer CR) resist this degradation better than systems with ad hoc interfaces.

---

## The Convergence Hypothesis

**Conjecture:** Optimizing a formal system for LLM-amenability — maximizing all ten axioms subject to faithfulness to the target domain — recovers designs that are independently well-motivated by mathematical and engineering principles. The design that is most navigable for an LLM is also the design that is most navigable for a human expert.

**Argument:** The semantic axioms independently select for mathematical structure: SD for deep theory, CR for categorical composition, CP for expressive constraints, CDB for mathematical foundations, RS for equational theories, LC for well-defined semantics. The mechanical axioms select for clarity and stability: CL for explicitness, RSing for canonicality, TSA for readable names, CI for timeless foundations. The conjunction of all ten selects for mathematically-grounded designs with clean, explicit, stable interfaces.

**Scope limitation:** The axioms measure *structural quality*, not *viability*. A system can score perfectly on all axioms and still be impractical due to cost, manufacturability, political feasibility, market timing, or other exogenous constraints. The Convergence Hypothesis claims that among viable systems, the axiom-optimal design is also the best-engineered design. It does not claim that axiom optimization alone produces viable systems.

**Corollary (Aesthetic Convergence):** A system optimized for LLM-amenability will "feel right" to a mathematically-minded practitioner, because both the LLM's navigability and the practitioner's sense of elegance are responding to the same underlying signal: the density and coherence of mathematical structure.

**Historical evidence:** In physics, every major theoretical advance (Newtonian gravity → general relativity, classical mechanics → quantum mechanics, separate forces → electroweak unification) increased the axiom scores of the succeeding theory relative to its predecessor. Superseded theories have identifiable axiom defects; their successors specifically repair those defects while preserving or raising all other scores. The history of physics is a monotonic ascent on the axiom landscape. If this pattern is general, the axioms are measuring something about the structure of knowledge itself, not just about the convenience of any particular reasoner.

---

## Applying the Framework

The axioms are a diagnostic tool for any formal system:

**For programming languages:** Score each axiom, identify the weakest, generate specific improvements ranked by effort and impact.

**For libraries and APIs:** Score the public interface, identify where semantic density can be increased (e.g., revealing hidden mathematical structure), where contextual locality can be improved (e.g., keeping type information near use sites), where representational singularity can be enforced (e.g., deprecating redundant API paths). Users of the library will increasingly work with LLM assistance; the axiom profile of the API directly affects their productivity.

**For hardware architectures:** Evaluate the ISA's computational model. Spatial/dataflow architectures score higher than von Neumann because their composition is topological (high CR), their types enforce connectivity (high CP), and their theoretical substrate is monoidal categories (high CDB). Name constructs for their mathematical identity, not their operational function — these names are latent space addresses that activate the LLM's knowledge of the underlying theory.

**For build systems, databases, protocols:** Every system has mathematical structure — dependency graphs, relational algebra, session types, configuration lattices. The axioms ask: is that structure visible in the types, connected to theory, and stable across time?

**For governance systems:** Constitutions are type systems. Decision procedures are composition operators. Rights are constraints that propagate through the decision space. The axioms diagnose structural defects: unanimity requirements are CP failures (one veto blocks everything), binary sanctions are LC failures (no graduated response), ad hoc decision procedures are CR failures (no algebraic composition). The axioms prescribe specific structural fixes without requiring political judgment about outcomes.

**For scientific theories:** The axioms measure navigability of a theory's formal structure. In physics, they reproduce the known open problems from structural analysis alone — the Standard Model's sub-9 scores correspond exactly to the hierarchy problem (CP), the product gauge group (CR), and the multiplicity of computational formalisms (RSing). Superseded theories have identifiable axiom defects that their successors repair. The axioms formalize what physicists have informally called "beauty" or "inevitability" in theoretical physics.

---

## Predictions

The framework generates specific, falsifiable predictions:

1. **LLMs generate correct Futhark code at higher rates than correct CUDA code** for equivalent tasks, despite more CUDA training data. (Tests CDB: mathematical structure compensates for smaller corpus.)

2. **Adding type signatures improves LLM correctness more in Haskell than in Python.** (Tests CP: rich types propagate more constraints.)

3. **LLM-generated cmake has higher error rates than LLM-generated Nix** for equivalent build tasks. (Tests the full framework: Nix scores higher on SD, CR, CP, RS, LC, CI.)

4. **Renaming identifiers from operational names to mathematical names** (`decode_unit` → `StackEffectHomomorphism`) measurably improves LLM assistance quality for the same code. (Tests SD × TSA: word-based mathematical names activate denser neighborhoods.)

5. **LLM-generated React code has higher rates of paradigm-mixing bugs than LLM-generated Elm code.** (Tests CI: React's temporal smearing vs Elm's stable architecture.)

6. **A programming language designed to maximize all ten axioms enables LLM code generation competitive with established languages** despite having zero training data, bootstrapping competence from mathematical adjacency alone. (Tests the entire framework.)

7. **LLM assistance quality for multi-system projects is dominated by the lowest-scoring component**, not the average. A project with excellent Haskell and terrible C scores closer to the C score overall. (Tests system-level composition: minimum dominates.)

8. **Fields of mathematics with higher axiom profiles (algebraic topology, category theory) see larger LLM-assisted productivity gains** than fields with lower profiles (combinatorics, analytic number theory), independent of training data volume. (Tests CDB applied to mathematical research.)

---

## Summary

| # | Axiom | Layer | Measures | Maximized By |
|---|-------|-------|----------|-------------|
| 1 | Semantic Density | Semantic | Knowledge activated per construct | Small vocabulary over deep theory |
| 2 | Compositional Regularity | Semantic | Lawfulness of composition | Categorical structure |
| 3 | Constraint Propagation | Semantic | Pruning power of constraints | Dependent/graded types; gauge symmetry; constitutional constraints |
| 4 | Cross-Domain Bridging | Semantic | Connections beyond the system's domain | Mathematical foundations |
| 5 | Refactoring Stability | Semantic | Soundness of meaning-preserving transformations | Equational theories, purity, gauge invariance |
| 6 | Latent-Space Continuity | Semantic | Smoothness of form→meaning mapping | Totality, no undefined behavior, no semantic traps |
| 7 | Contextual Locality | Mechanical | Attentional distance to required context | Pure functions, local types, explicit arguments |
| 8 | Representational Singularity | Mechanical | Canonicality of expression | One way per semantic intent (for divergent constructs) |
| 9 | Token-Semantic Alignment | Mechanical | Tokenizer compatibility | Word-based identifiers over symbolic operators |
| 10 | Chronological Invariance | Mechanical | Temporal stability of semantics | Stable standards, mathematical foundations, low drift |
