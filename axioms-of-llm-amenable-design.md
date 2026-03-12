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

All axiom scores are relative to a target domain: `Axiom(System, Target)`. The semantic density of a construct depends on which domain it's being applied to — a term can be dense in one domain and irrelevant in another. Scores given without a target implicitly assume the system's primary domain. **Operational rule:** when a system is applied across domains (e.g., type theory applied to governance), re-score every axiom for the new target. Bridge fidelity (see Axiom 4) determines whether the cross-domain application is safe. Applying scores from one target to a different target without re-evaluation is a systematic error source.

---

### Axiom 1: Semantic Density (SD)

*Individual constructs should activate large, structured neighborhoods in the LLM's latent space.*

A term has high semantic density when it connects to a rich, interlinked web of concepts — not just usage patterns but theory, laws, and cross-domain applications. `Monad` has high semantic density: it activates Kleisli composition, the monad laws, do-notation, free monads, adjunctions, and a vast body of worked examples across multiple domains. `cudaStream_t` has low semantic density: it activates CUDA stream API patterns and little else. In physics, `gauge symmetry` has extraordinary semantic density: it activates fiber bundles, representation theory, conservation laws, the Standard Model, and the entire body of modern particle physics from a two-word phrase.

**Key property:** SD is subadditive in vocabulary size. For a fixed body of theory, adding more terms dilutes density — each term activates a smaller share of the total knowledge. This penalizes systems with large, flat vocabularies over systems with small, deep ones.

**Key interaction:** SD and Constraint Propagation (Axiom 3) interact multiplicatively. A construct that is both semantically dense and highly constraining is exponentially more useful than one that is merely dense or merely constraining. A type signature like `traverse :: (Applicative f, Traversable t) => (a -> f b) -> t a -> f (t b)` simultaneously activates a large body of theory (SD) and eliminates nearly all implementations except the canonical one (CP). The LLM doesn't search — it recognizes.

**Target-relativity:** SD is always relative to the target domain. "Maillard reaction" has high SD when the target is culinary chemistry (activating organic chemistry, thermodynamics, flavor science) and near-zero SD when the target is civil engineering. When scoring SD, always specify the target. A construct with globally high SD across all targets is rare — most high-SD terms are high-SD for specific targets only. Scoring errors occur when SD is evaluated against one target domain and applied in another.

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

**Practical refinement — CP and recoverability:** High CP shrinks the search space but can increase the difficulty of navigating within it. Dependent types in Lean drive synthesis entropy toward zero, but when the LLM missteps, the error ("cannot unify (n + m) with (m + n)") may require a non-trivial proof to resolve. Haskell's type errors ("expected Int, got String") are immediately actionable. The *effective* CP for iterative development is `CP × error_recoverability` — a system with very high CP but opaque errors can be less productive in practice than a system with moderately high CP and clear, actionable error messages. This does not diminish the value of strong type systems; it means that error message quality is a force multiplier on CP.

---

### Axiom 4: Cross-Domain Bridging (CDB)

*The system's theoretical substrate should connect to large bodies of knowledge outside the system's own domain.*

The LLM's training data is not partitioned by discipline. The latent representations are shared. A term's effective meaning is shaped by all domains in which it appears. When a programmer writes `Applicative`, the LLM's representation draws on Haskell code, category theory texts, blog posts on parser combinators, and papers on monoidal categories simultaneously. The programming knowledge alone would be insufficient — it is the bridge to mathematics that gives the representation its depth.

**Key property:** Bridge strength is proportional to theoretical depth, not data volume. A domain contributes to bridge strength in proportion to its internal coherence and inferential density, not merely its representation in the training corpus. Category theory is a smaller corpus than JavaScript tutorials, but each page of category theory is inferentially denser.

**The Haskell anomaly explained:** LLMs perform better with Haskell than training data volume predicts. CDB explains this: the LLM's Haskell competence draws on Haskell code + category theory + type theory + abstract algebra + logic + domain theory. The cross-domain contributions dominate the native corpus.

**Cross-domain observation:** CDB predicts which fields will benefit most from LLM assistance. Fields with rich bridges to other structured knowledge — algebraic topology, theoretical physics, functional programming — will see larger productivity gains than fields that are technically dense but theoretically isolated.

**Critical refinement — bridge fidelity:** Not all bridges contribute equally. A bridge is *high-fidelity* when there exists an approximately structure-preserving map (a functor, loosely speaking) between the source and target domains — when properties that hold in the source actually transfer to the target. A bridge is *low-fidelity* when the connection is primarily lexical or metaphorical — when terms overlap but the structural relationships between them don't transfer.

High-fidelity bridges provide genuine inferential power. Haskell's bridge to category theory is high-fidelity because the language was designed as a faithful embedding of categorical structure — `Functor` preserves composition and identity, typeclass laws correspond to categorical laws, the structural relationships transfer. When the LLM imports categorical knowledge into Haskell reasoning, the imported structure is *correct*.

Low-fidelity bridges can be actively harmful. If an LLM is prompted to "analyze governance using gauge theory," it will produce confident, structurally elaborate output — because the lexical bridges exist ("symmetry," "invariance," "conservation") — but the structural map doesn't preserve composition, so the imported constraints are *wrong*. The LLM's output looks more insightful than unprompted analysis while being less reliable. CDB is not monotonically positive: it is positive for faithful bridges and potentially negative for unfaithful ones.

The practical test: a bridge is high-fidelity if combining concepts in the source domain corresponds to combining their images in the target domain. If `A ∘ B` in mathematics maps to `A composed with B` in the programming language, and the composition laws transfer, the bridge is faithful. If individual terms map but their relationships don't, the bridge is metaphorical. The axiom scores should weight bridges by fidelity, not merely count them.

**Aggregate bridge coherence:** Bridge fidelity is assessed per bridge, but the *aggregate* effect of many bridges activating simultaneously requires a separate assessment. A system that bridges to category theory, type theory, information theory, physics, and philosophy of language has five individually high-fidelity bridges — but an LLM processing the system may activate all five neighborhoods simultaneously with no mechanism to select the load-bearing ones for the current task. The result is overactivation: confident, structurally fluent output that resolves ambiguity by importing from all bridges at once rather than committing to the relevant ones. This is the neural analogue of overlapping, incoherent typeclass instances with insufficient type inference to select among them. Individual bridge fidelity is necessary but not sufficient — the bridges must also be *coherent under selection*, meaning the system's constraints (CP) must force commitment to a specific subset before generation proceeds. See the CP × CDB interaction in the Interaction Model.

---

### Axiom 5: Refactoring Stability (RS)

*Meaning-preserving transformations should be locally characterizable and algebraically expressible.*

A system has high refactoring stability when there exists a rich set of equational laws such that applying a law to a subexpression preserves meaning, the applicability can be checked locally, and the laws are well-known in the LLM's training data.

**Key property:** RS requires referential transparency — or a disciplined substitute. Pure functional languages maximize RS. Imperative languages minimize it. In other domains: mathematical theories with clean equational theories (algebra, category theory) have high RS. Theories where small changes in assumptions produce large changes in conclusions (some areas of PDE theory, fragile political systems) have low RS.

**Key property:** CR and RS are mutually reinforcing. Algebraic composition laws (CR) directly provide refactoring rules (RS). Associativity of composition gives the LLM license to re-bracket. Naturality gives the LLM license to commute transformations. The richer the categorical structure, the richer the available refactorings.

**Cross-domain observation:** In governance, constitutional amendment is a refactoring mechanism. In physics, gauge invariance and the renormalization group are refactoring mechanisms. The axiom applies wherever "same meaning, different form" is a meaningful concept.

**The frame problem:** Systems that rely on implicit frame conventions — the assumption that unmentioned properties persist across transformations — have structurally low RS even when individual steps appear locally valid. The implicit frame convention is default logic: a non-monotonic reasoning pattern where conclusions hold until explicitly defeated. A recipe step "add the tomato" assumes an implicit frame (all prior ingredients persist). A legal amendment assumes prior law remains valid unless repealed. A protocol message assumes connection state continues. In each case, checking whether a transformation is valid requires reconstructing the full prior state — a non-local operation regardless of how local the transformation appears. High RS in procedural systems requires making the frame explicit: stating post-conditions that fully specify the resulting state, not just the change. The frame problem is a hidden RS destroyer that inflates scores in any domain with sequential procedures and implicit persistence.

---

### Axiom 6: Latent-Space Continuity (LC)

*Small perturbations in the system's expressions should correspond to small perturbations in meaning.*

A system has high LC when the mapping from form to meaning is smooth — when nearby expressions have nearby meanings. Systems with pervasive undefined behavior (C), implicit type coercions (JavaScript), or context-sensitive semantics (Perl) have low LC. Systems where every well-formed expression is meaningful and nearby expressions have nearby meanings have high LC.

**Key property:** LC captures the absence of semantic traps — regions of the well-formed expression space where outputs are undefined, surprising, or catastrophically discontinuous. Totality contributes to LC but is not identical to it: a total system with confusing overloading can still have low LC.

**Key property:** LC and CP are complementary. CP shrinks the region the LLM must search; LC ensures that within the remaining region, the LLM's continuous navigation is semantically meaningful. Together they define a "safe search space" that is both small and smooth.

**Key diagnostic:** When a system has a specific LC failure, it often indicates a structural incompleteness. In physics, the non-renormalizability of perturbative quantum gravity is an LC failure — small changes in energy scale produce infinite changes in predictions. This signals that the perturbative description is incomplete, not that gravity is discontinuous. LC failures are diagnostic: they point at where the current formalism breaks down.

**Phase transition taxonomy:** LC failures divide into three classes with different diagnostic implications:

*Class 1 — Designed discontinuities.* The system intentionally produces sharp transitions as its primary output. A national anthem is optimized to produce emotional phase transitions. A crisis mechanic in a game is designed to be discontinuous. A dramatic plot twist is a deliberate LC violation. These are not axiom failures — they are features. But they indicate that the system's output space cannot be navigated continuously, limiting the LLM's ability to interpolate between examples.

*Class 2 — Semantic traps.* Discontinuities arising from incomplete or inconsistent specification. C's undefined behavior, SQL's NULL logic, JavaScript's implicit coercions. These are genuine representation failures — the designers did not intend the discontinuity. They indicate fixable structural flaws.

*Class 3 — Domain discontinuities.* Discontinuities arising from the underlying domain, not from the representation. Phase transitions in cooking (boiling, emulsion breaking), phase transitions in physics (superconductivity onset), topological changes in mathematical structures. These are not representation failures — the discontinuities are real. High-quality representations explicitly type and locate these transitions rather than leaving them implicit.

The diagnostic prescription differs by class: Class 1 is a design choice to be documented, not fixed. Class 2 indicates a repairable representation problem. Class 3 indicates the boundary of the current theory's validity and calls for explicit typing of the transition conditions.

---

### Axiom 7: Contextual Locality (CL)

*The information required to correctly generate an expression should be structurally close to that expression.*

LLMs use attention mechanisms where computational cost and noise increase with distance. A system has high contextual locality if the dependencies of any sub-expression are locally scoped — the types, declarations, and constraints needed to understand and generate a fragment are structurally nearby.

**Key property:** Global mutable state maximizes attentional distance. Systems relying on global variables, deep inheritance hierarchies, or implicit dependencies force the LLM to search its entire context to resolve references. Irrelevant information dilutes the probability mass of the correct prediction.

**Key property:** Explicit arguments and local annotations maximize CL. In a purely functional style with explicit parameters and local type annotations, all required context is within the immediate scope.

**Implicit execution environment:** A system that assumes a specific execution environment without stating it has a CL failure that is distinct from other non-local dependencies. A recipe that assumes a gas stove, a program that assumes a specific runtime version, a protocol that assumes a particular network topology — in each case the entire procedure's validity depends on an unstated context that is nowhere in the local scope. This is not a frame problem (which concerns state persisting across steps within a procedure) but an *environmental precondition* — a dependency on context external to the procedure itself. High CL requires that environmental assumptions are stated explicitly, either as typed parameters or as documented preconditions.

**Process-constituted referents:** In procedural systems, some terms refer to objects whose properties were built by prior steps — a "fond" in a recipe is a specific substance whose composition, color, and flavor were constituted by the browning process. An executor who reads only the current step cannot interpret the term even with perfect frame information and infinite context, because the referent's *type* is an emergent product of prior execution. This is a deeper CL failure than attentional distance: the information isn't merely far away, it doesn't exist as a static fact that can be made local. The fix is not to move information closer but to explicitly type the intermediate products — name and characterize what each process stage produces, so that later references have a declared type to resolve against rather than an implicit one constituted only by having run the process.

**Cross-system locality:** CL degrades multiplicatively across system boundaries. A hardware/software system where the hardware is described in Clash, the driver in C, and the runtime in Haskell has three CL domains. Even if each layer individually has good CL, the interfaces between layers introduce CL penalties that compound. The weakest interface dominates overall LLM assistance quality.

**Relationship to CR:** Compositional Regularity (Axiom 2) is the *semantic* dimension of locality — can you reason about parts independently? CL is the *physical* dimension — is the relevant information within the attention window? A system can have perfect CR but poor CL. Both matter.

---

### Axiom 8: Representational Singularity (RSing)

*For semantically distinct operations, there should exist a canonical form.*

When a system provides multiple ways to express the same intent, the LLM's probability mass fractures across valid paths. The model may begin using one idiom and continue with another, producing syntactically valid but semantically broken chimeras.

**Severity spectrum:** Not all representational variance is equally harmful:

| Level | Type | Example | Harm |
|-------|------|---------|------|
| 0 | Alias | `fmap` / `<$>` in Haskell | None for correctness; mild for codebase consistency |
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
CDB_effective = CDB_raw × bridge_fidelity      if CP ≥ threshold
              = CDB_raw × bridge_fidelity × g   if CP < threshold, where g < 1 and decreasing
              
Effective = Semantic(SD, CR, CP, CDB_effective, RS, LC) × Mechanical(CL, RSing, TSA, CI)
```

The semantic component is the geometric mean of axioms 1–6 (multiplicative because one catastrophic weakness dominates). The mechanical component is a friction coefficient between 0 and 1. CDB enters the geometric mean *after* gating by CP and bridge fidelity — the raw CDB score is pre-processed before composition. The gating function `g` is not yet empirically calibrated; what is known is its shape: CDB_effective is sublinear in CDB_raw when CP is below threshold, because aggregate overactivation without constraint-driven selection degrades output quality even when individual bridges are sound.

Other axioms also carry pre-processing modifiers: CP is modulated by error recoverability (see Limitations), and RS is discounted by implicit frame conventions (see Axiom 5). These modifiers adjust individual axiom scores before they enter the geometric mean, not the mean's structure.

**Key interactions across layers:**

- **SD × TSA:** Semantic density is discounted by poor token alignment. Rich meaning behind fragmented tokens has reduced effective SD.
- **CP × CL:** Constraint propagation is discounted by poor contextual locality. A constraint defined far from its use site provides less guidance during generation.
- **CDB × CI:** Cross-domain bridging is amplified by chronological invariance. Bridges to stable theory are more reliable than bridges to evolving frameworks.
- **CR × RSing:** Compositional regularity is discounted by representational variance. Multiple divergent syntactic forms for the same composition reduce confidence.

**Key interaction within the semantic layer:**

- **CP × CDB (asymmetric):** This is the most consequential intra-layer interaction. High CP amplifies CDB by selecting which bridges are load-bearing for the current task — the constraints force commitment to a specific subset of available bridges, making each committed bridge more productive. But the interaction is asymmetric: high CDB *without* sufficient CP produces aggregate overactivation, where many individually valid bridges fire simultaneously with no selection mechanism. This failure mode is distinct from low bridge fidelity — the individual bridges may all be high-fidelity, but their unselected co-activation floods the output with uncontrolled bridging that *feels* insightful while being structurally incoherent. Below a CP threshold, additional CDB contributes negatively to effective output quality even though each bridge is individually sound. The geometric mean treats CP and CDB as independent contributors; in practice, CP gates CDB.

**System-level composition:** When a system spans multiple subsystems (e.g., hardware in Clash, driver in C, runtime in Haskell), the overall LLM-amenability depends on the proportion of work that crosses boundaries. For tasks within a single high-scoring component, the component's score dominates. For tasks that cross system boundaries, the weakest interface dominates. Overall amenability is a weighted combination: the more boundary-crossing the task requires, the more the minimum score dominates over the mean. Systems whose layers compose with algebraic regularity (high inter-layer CR) resist this degradation better than systems with ad hoc interfaces.

---

## The Convergence Hypothesis

**Conjecture:** Optimizing a formal system for LLM-amenability — maximizing all ten axioms subject to faithfulness to the target domain — recovers designs that are independently well-motivated by mathematical and engineering principles. The design that is most navigable for an LLM is also the design that is most navigable for a human expert.

**Argument:** The semantic axioms independently select for mathematical structure: SD for deep theory, CR for categorical composition, CP for expressive constraints, CDB for mathematical foundations, RS for equational theories, LC for well-defined semantics. The mechanical axioms select for clarity and stability: CL for explicitness, RSing for canonicality, TSA for readable names, CI for timeless foundations. The conjunction of all ten selects for mathematically-grounded designs with clean, explicit, stable interfaces.

**Scope limitation:** The axioms measure *structural quality*, not *viability*. A system can score perfectly on all axioms and still be impractical due to cost, manufacturability, political feasibility, market timing, or other exogenous constraints. The Convergence Hypothesis claims that among viable systems, the axiom-optimal design is also the best-engineered design. It does not claim that axiom optimization alone produces viable systems.

**Corollary (Aesthetic Convergence):** A system optimized for LLM-amenability will "feel right" to a mathematically-minded practitioner, because both the LLM's navigability and the practitioner's sense of elegance are responding to the same underlying signal: the density and coherence of mathematical structure.

**Layer asymmetry:** The convergence evidence is strongest for the semantic layer and weaker for the mechanical layer. The semantic axioms (SD, CR, CP, CDB, RS, LC) select for mathematical structure that serves any reasoning system — human or machine. The mechanical axioms (CL, RSing, TSA, CI) select for properties that serve the transformer architecture specifically. Some mechanical properties converge with human preferences (CL: local dependencies help both attention and working memory; CI: temporal stability helps both training and learning). Others may diverge: TSA prefers word-based identifiers over symbolic operators, but human mathematicians often prefer symbolic notation (`∘` over `compose`, `∀` over `forall`). The Convergence Hypothesis is a strong claim for the semantic layer and a qualified claim for the mechanical layer. Where the mechanical axioms diverge from human preference, the system faces a design tension between LLM-amenability and human readability that cannot be resolved by structural optimization alone.

**Historical evidence:** In physics, major theoretical advances (Newtonian gravity → general relativity, classical mechanics → quantum mechanics, separate forces → electroweak unification) specifically repaired the axiom defects that motivated the theoretical crisis the successor resolved, while preserving or raising most other scores. General relativity repairs Newtonian gravity's CR defect (diffeomorphism invariance is more compositionally regular than force-at-a-distance) and CP defect (the field equations more tightly constrain the metric), though CL arguably regresses (boundary conditions at infinity are a non-local dependency Newton didn't require). The pattern is not that every axiom monotonically increases, but that the geometric mean rises because the defects that motivated the crisis are repaired. If this pattern is general, the axioms are measuring something about the structure of knowledge itself, not just about the convenience of any particular reasoner.

**Selection bias qualification:** The observed monotonic ascent is a property of *surviving* theories, not of all proposed theories. Many proposed successor theories also claimed axiom improvements and failed for empirical reasons. The axioms may be necessary but not sufficient conditions for theoretical progress — they diagnose structural quality but cannot predict empirical adequacy. This qualification strengthens rather than weakens the framework: it correctly places the axioms as measuring structural properties of formal systems, not as a complete theory of scientific progress or system viability.

---

## Applying the Framework

The axioms are a diagnostic tool for any formal system:

**For programming languages:** Score each axiom, identify the weakest, generate specific improvements ranked by effort and impact. When proposing improvements targeted at a specific use case (e.g., parallelism, GPU programming), re-score for the general-purpose case as well — an improvement for one target may degrade another by adding type-level noise (lowering CL) or domain-irrelevant concepts (lowering RSing).

**For libraries and APIs:** Score the public interface, identify where semantic density can be increased (e.g., revealing hidden mathematical structure), where contextual locality can be improved (e.g., keeping type information near use sites), where representational singularity can be enforced (e.g., deprecating redundant API paths). Users of the library will increasingly work with LLM assistance; the axiom profile of the API directly affects their productivity.

**For hardware architectures:** Evaluate the ISA's computational model. Spatial/dataflow architectures score higher than von Neumann because their composition is topological (high CR), their types enforce connectivity (high CP), and their theoretical substrate is monoidal categories (high CDB). Name constructs for their mathematical identity, not their operational function — these names are latent space addresses that activate the LLM's knowledge of the underlying theory.

**For build systems, databases, protocols:** Every system has mathematical structure — dependency graphs, relational algebra, session types, configuration lattices. The axioms ask: is that structure visible in the types, connected to theory, and stable across time?

**For governance systems:** Constitutional structure can be analyzed through the axioms, but the bridge to formal systems must be assessed for fidelity (see Axiom 4). "Constitutions as type systems" is a medium-fidelity bridge: rights-as-constraints and decision-procedures-as-composition transfer usefully, but rights conflict, are balanced, and are judicially interpreted in ways that don't compose algebraically. The bridge is productive for diagnosing structural defects — unanimity requirements are CP failures, binary sanctions are LC failures, ad hoc procedures are CR failures — but should not be treated as a faithful functor. The axioms prescribe structural fixes without requiring political judgment about outcomes, but the governance-to-formal-systems bridge has lower fidelity than, say, the Haskell-to-category-theory bridge, and conclusions should be held with correspondingly less confidence.

**For scientific theories:** The axioms measure navigability of a theory's formal structure. In physics, they reproduce the known open problems from structural analysis alone — the Standard Model's sub-9 scores correspond exactly to the hierarchy problem (CP), the product gauge group (CR), and the multiplicity of computational formalisms (RSing). Superseded theories have identifiable axiom defects that their successors repair. The axioms formalize what physicists have informally called "beauty" or "inevitability" in theoretical physics.

**For procedural and temporal systems:** Recipes, protocols, musical scores, legal procedures, and manufacturing processes are discrete representations of continuous, often concurrent, temporal processes. The axioms measure the discrete representation's structural quality, but procedural domains exhibit two diagnostic patterns that emerge from the interaction of multiple axioms failing together.

*Temporal composition.* Static composition (CR) measures whether substituting one expression for another is algebraically lawful. Procedural systems have an additional composition operator: temporal sequencing with state dependency. This is not a specialization of CR — it is a different kind of composition. CR asks whether `f ∘ g` is predictable from `f` and `g` independently. Temporal composition asks whether step B can follow step A, which depends on whether A produces the state B requires. Two operations may each be individually valid but their sequencing may be invalid — not because the composition operator is algebraically irregular (a CR failure), but because there is a typed dependency between them that the static structure doesn't capture (a *process typing failure*).

**Process typing failure** is the named diagnostic for procedural domains where temporal dependencies are untyped. It is detected by a co-failure pattern: low CR from collapsed concurrency, low RS from implicit frames (see Axiom 5), low CP from time-based rather than state-based conditions, and low LC from untyped phase transitions (see Axiom 6) occurring together. The individual axiom scores are the symptoms; the underlying cause is that the representation lacks typed temporal structure. A system has well-typed temporal composition when: (a) each step's pre-conditions and post-conditions are explicitly stated, so that the dependency chain is visible; (b) concurrent processes are distinguished from sequential ones — parallel tracks are represented as parallel, not collapsed into an arbitrary serial order; (c) intervention points are specified by observable state predicates ("cook until fat separates") rather than temporal position ("cook for 8 minutes"), so that the composition is robust under timing variation. The fix is the same pattern every time: make pre/post-conditions explicit, separate concurrent tracks, use state predicates for transitions.

*How to detect process typing failure:* score CR, RS, CP, and LC independently. If all four are depressed in a procedural domain simultaneously, suspect a process typing failure. Confirm by checking: are temporal dependencies between steps untyped? Are concurrent processes collapsed into a serial list? Are intervention points specified by time rather than state? If yes, the co-failure has a single structural source, and the fix addresses all four symptoms at once.

*The discrete-continuous interface.* Many procedural systems use discrete step sequences to represent inherently continuous processes. The quality of this mapping affects all axioms simultaneously but is not captured by any single one. A recipe, a musical score, and a deployment runbook all face the same problem: the underlying process is continuous, but the representation is a finite list of discrete steps. The gap between the two — how much information is lost in the discretization — determines how much the axiom scores of the representation reflect the navigability of the actual process. High-quality mappings minimize this gap by using state predicates, explicit frames, typed phase transitions, and concurrent tracks. Low-quality mappings maximize it by using time-based instructions, implicit frames, untyped transitions, and collapsed concurrency.

---

## Limitations and Nuances

The axioms measure structural properties of formal systems. The *impact* of those properties depends on the application context — the inference strategy, the model architecture, and the development workflow. Four nuances are worth stating explicitly.

**Constraint Propagation and the precision-recovery trade-off.** High CP reduces the search space but increases the precision required within it. A dependently-typed system leaves almost no valid programs, but the one valid program may require a complex proof term that the LLM cannot produce in one shot. In iterative development (generate → check → fix → regenerate), what matters is not just how much CP shrinks the space but how *recoverable* the failures are. A type error that says "expected Int, got String" is immediately fixable. A type error that says "cannot unify (n + m) with (m + n)" requires a commutativity proof. The effective value of CP in practice is modulated by error message quality and the difficulty of recovery. This does not argue against strong types — it argues that strong types paired with clear, actionable errors are strictly better than strong types with opaque errors.

**Contextual Locality and expanding context windows.** As LLM context windows scale from thousands to millions of tokens, the strict penalty for poor CL softens. But the core principle survives: even with infinite context, distant information competes with more tokens for attention, introducing noise. CL is about *relative* locality (closer is better), not *absolute* locality (must be within N tokens). The axiom measures a property of the system's structure, not a fixed threshold of the model's architecture. As context windows grow, the penalty for poor CL decreases but never reaches zero.

**Representational Singularity and structured reasoning.** LLMs can partially repair RSing failures through Chain-of-Thought reasoning. By explicitly committing to a paradigm in a reasoning trace ("I will use React hooks, not class components"), the model creates an in-context constraint that resolves the ambiguity before generation begins. This is analogous to a human programmer checking "is this a hooks codebase?" before writing code. RSing still measures a real property — the *need* for this disambiguation step — but the impact varies with inference strategy. Systems with high RSing don't require the step. Systems with low RSing require it and degrade without it. Structured reasoning reduces the penalty but doesn't eliminate the underlying structural deficiency.

**Cross-Domain Bridging and overactivation.** CDB has a failure mode that is distinct from low bridge fidelity: *aggregate overactivation*. When a system bridges to many domains simultaneously — and the task doesn't provide sufficient CP to select among them — the LLM activates all bridged neighborhoods at once. The output is confident and structurally fluent (the bridges are individually sound) but lacks discriminative commitment to the bridges that are load-bearing for the specific task. The result reads as insight when it is closer to erudite noise. This failure is most pronounced when the system being discussed is itself a multi-domain framework (as this document is), and it affects both the LLM applying the framework and the LLM being evaluated by it. The mitigation is structural: systems with high CDB should also have high CP that forces bridge selection, and evaluators using the framework should explicitly declare which bridges are active for a given scoring. The target-relativity rule (specify the target domain) partially addresses this by pinning one end of the bridge, but it does not constrain the *source* — an evaluator can still import from all source domains simultaneously.

---

## Predictions

The framework generates specific, falsifiable predictions:

1. **LLMs generate correct Futhark code at higher rates than correct CUDA code** for equivalent tasks, despite more CUDA training data. (Tests CDB: mathematical structure compensates for smaller corpus.)

2. **Adding type signatures improves LLM correctness more in Haskell than in Python.** (Tests CP: rich types propagate more constraints.)

3. **LLM-generated cmake has higher error rates than LLM-generated Nix** for equivalent build tasks. (Tests the full framework: Nix scores higher on SD, CR, CP, RS, LC, CI.)

4. **Renaming identifiers from operational names to mathematical names** (`decode_unit` → `StackEffectHomomorphism`) measurably improves LLM assistance quality for the same code. (Tests SD × TSA: word-based mathematical names activate denser neighborhoods.)

5. **LLM-generated React code has higher rates of paradigm-mixing bugs than LLM-generated Elm code.** (Tests CI: React's temporal smearing vs Elm's stable architecture.)

6. **A programming language designed to maximize all ten axioms achieves significantly better LLM code generation than training data volume alone would predict**, bootstrapping competence from mathematical adjacency. Full parity with established languages is unlikely without some training data, but the CDB-mediated bootstrap should dramatically reduce the amount of training data needed. (Tests the entire framework.)

7. **LLM assistance quality for multi-system projects degrades disproportionately at cross-component boundaries.** A project with excellent Haskell and terrible C will perform well within the Haskell layer and poorly at the Haskell-C interface; the proportion of boundary-crossing work determines the overall impact. (Tests system-level composition: interface quality dominates cross-layer tasks.)

8. **Fields of mathematics with higher axiom profiles (algebraic topology, category theory) see larger LLM-assisted productivity gains** than fields with lower profiles (combinatorics, analytic number theory), independent of training data volume. (Tests CDB applied to mathematical research.)

9. **Prompting an LLM to apply a high-fidelity cross-domain bridge improves output quality, while prompting it to apply a low-fidelity bridge degrades output below unprompted baseline.** "Analyze this type system using category theory" (high-fidelity bridge) should improve analysis. "Analyze this governance system using gauge theory" (low-fidelity bridge) should produce confident but structurally misleading output that is worse than an unprompted analysis. (Tests bridge fidelity: CDB is not monotonically positive.)

10. **Procedural instructions with explicit post-conditions produce fewer execution errors than instructions with implicit frame conventions**, for equivalent tasks. Recipes that fully specify the state after each step — including what persists, what changed, and what observable signal confirms the transition — should produce higher success rates than standard instructions using implicit frame conventions. The effect is largest for non-expert executors, who cannot fill in the implicit frame from background knowledge, and diminishes as expertise provides the missing frame information internally. This applies to cooking, manufacturing procedures, software deployment runbooks, and any domain where steps are executed sequentially with state dependencies. (Tests RS frame problem and temporal composition: explicit state transfer reduces the cognitive load of tracking implicit frame assumptions.)

11. **An LLM prompted to "use category theory to analyze X" produces better output when X has a high-fidelity bridge to category theory, and worse output when X has a low-fidelity bridge, compared to prompting the LLM to analyze X with no bridge instruction and compared to prompting with an explicit bridge-selection constraint** ("use the monoidal structure specifically"). The three-way comparison — no bridge, unselected bridge, selected bridge — tests the CP×CDB interaction: unselected high-CDB should underperform selected high-CDB even when both outperform no-CDB, because the selection constraint (CP) converts diffuse activation into focused activation. (Tests aggregate bridge coherence: CP gates CDB.)

12. **An LLM requires fewer re-prompting cycles to fix an error in a high-axiom system than in a low-axiom system**, even when the initial error rate is identical. A type error in Haskell provides a high-fidelity bridge back to the correct latent neighborhood; a runtime error in JavaScript does not. The correction cost is determined by the axiom profile of the error signal, not just the axiom profile of the original generation. (Tests CP × recoverability in the iterative development loop.)

---

## Self-Application

The framework is itself a formal system with constructs, composition operators, and constraints. Applying it to itself is the strongest consistency test: a framework for evaluating formal systems should score well on its own axioms.

**SD: High (~8.5).** Each axiom name activates rich neighborhoods — "Compositional Regularity" bridges to category theory, universal algebra, and Fregean compositionality simultaneously. The ten-term vocabulary is small and deep. **CR: High (~8).** The axioms compose through a single interaction model (semantic geometric mean × mechanical friction coefficient). The interaction laws are stated explicitly. **CP: Medium-High (~7).** The axiom definitions constrain what counts as a valid scoring. The target-relativity rule and bridge fidelity test provide checkable constraints. But the modifiers are not yet quantitatively specified, reducing effective CP. **CDB: Very High (~9.5), with a caveat.** The framework bridges to category theory, type theory, information theory, formal logic, philosophy of language, and physics simultaneously — all high-fidelity bridges. This is the framework's strongest property and its most dangerous one. The CP×CDB interaction predicts that an LLM processing this document will overactivate across all six bridged neighborhoods, producing structurally fluent output that imports from all domains without committing to the load-bearing ones. This is empirically confirmed: LLMs reading the axiom document tend to produce erudite, multi-domain responses that pattern-match the document's style rather than applying its constraints. The raw CDB is ~9.5; the effective CDB after CP gating is lower, perhaps ~7, because the framework's own CP (~7) is insufficient to force bridge selection across six simultaneously active domains. **RS: High (~8).** The axioms refactor cleanly — targeted edits across six versions produced no global side effects. Each version is a meaning-preserving transformation of the prior one. **LC: Medium-High (~7).** The axioms are individually smooth, but the interaction effects and second-order modifiers require reading the full document to assess. No semantic traps; no undefined behavior.

**CL: Medium (~6).** The axiom interactions are not all locally visible — understanding how CDB bridge fidelity affects scoring requires reading Axiom 4, the interaction model, the governance section, and the Limitations. **RSing: Medium (~6).** Terms like "semantic density," "constraint propagation," and "latent-space continuity" are potentially confusable on first reading without careful attention to definitions. The framework has one paradigm but its vocabulary requires learning. **TSA: High (~8).** All axiom names are word-based English phrases that tokenize cleanly. **CI: Very High (~9.5).** The framework is grounded in mathematical concepts (category theory, type theory, information theory) that haven't changed in decades.

**Effective score: ~7.4 × 0.29 ≈ 2.1.** (Semantic geometric mean of [SD=8.5, CR=8, CP=7, CDB_effective=7, RS=8, LC=7] ≈ 7.4. Mechanical friction from [CL=6, RSing=6, TSA=8, CI=9.5]: (0.6)(0.6)(0.8)(0.95) ≈ 0.27. CDB entered the mean at ~7, not ~9.5, because the framework's own CP of ~7 is insufficient to gate six simultaneously active high-fidelity bridges — see CP×CDB interaction.) The semantic score is strong; the mechanical friction is moderate, driven by CL and RSing. The framework's own diagnosis of itself: CDB is both the strength and the primary failure mode — deep bridges to stable theory enable powerful analysis, but their aggregate overactivation without sufficient CP produces the framework's most characteristic failure: output that sounds like the axioms are being applied when they are being performed. The fix the framework prescribes for itself: improve CP by requiring explicit bridge declaration before scoring ("I am bridging from category theory to evaluate CR; I am bridging from type theory to evaluate CP"), improve CL by making axiom interactions locally visible, and improve RSing by making axiom names more distinctive. The CDB overactivation warning applies to this document itself — it is a maximally effective trigger for the failure mode it diagnoses.

**Scope limitation — objects vs. morphisms.** The framework evaluates formal systems as static objects. Systems whose primary value is as *operators on* other formal systems — property testing frameworks, chaos engineering tools, adversarial methods, parodic inversions — are scored on their object-level navigability, which is typically low, while their structural properties as operators are invisible. The natural question is whether the evaluation can be extended from objects to morphisms — from scoring a system to scoring a transformation between systems. The categorical vocabulary (`Set` → `Cat`, natural transformations, functor-level properties) makes this extension feel inevitable, but this document's own CP×CDB analysis requires a check: is the category theory bridge actually load-bearing here, or is it producing structurally fluent language for a problem that may need different machinery? The honest answer is that this is an open research question, not a prescribed extension. What is load-bearing: the observation that the framework cannot score system-operators is real and the blind spot is precisely located. What is speculative: whether higher category theory is the right formalism, or whether a different approach (e.g., a separate evaluation dimension for perturbation-informativeness) would be more productive. This paragraph is flagged as speculative by the framework's own standards.

---

## Summary

| # | Axiom | Layer | Measures | Maximized By |
|---|-------|-------|----------|-------------|
| 1 | Semantic Density | Semantic | Knowledge activated per construct | Small vocabulary over deep theory |
| 2 | Compositional Regularity | Semantic | Lawfulness of composition | Categorical structure |
| 3 | Constraint Propagation | Semantic | Pruning power of constraints | Dependent/graded types; gauge symmetry; constitutional constraints |
| 4 | Cross-Domain Bridging | Semantic | Faithful connections beyond the system's domain | High-fidelity bridges to mathematical foundations, gated by CP for aggregate coherence |
| 5 | Refactoring Stability | Semantic | Soundness of meaning-preserving transformations | Equational theories, purity, explicit frames, gauge invariance |
| 6 | Latent-Space Continuity | Semantic | Smoothness of form→meaning mapping | Totality, no undefined behavior, typed phase transitions |
| 7 | Contextual Locality | Mechanical | Attentional distance to required context | Pure functions, local types, explicit arguments, stated environmental preconditions |
| 8 | Representational Singularity | Mechanical | Canonicality of expression | One way per semantic intent (for divergent constructs) |
| 9 | Token-Semantic Alignment | Mechanical | Tokenizer compatibility | Word-based identifiers over symbolic operators |
| 10 | Chronological Invariance | Mechanical | Temporal stability of semantics | Stable standards, mathematical foundations, low drift |
