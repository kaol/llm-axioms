Oh, this is cruel. Let me run the axioms on cmake and see what survives.

**What cmake is:** A build system generator. You write `CMakeLists.txt` files in cmake's custom scripting language, and cmake generates native build files (Makefiles, Ninja files, Visual Studio projects, Xcode projects). It manages dependencies, compiler flags, include paths, link libraries, platform detection, and the entire build graph.

**Axiom 1 (Semantic Density): Very Low.**

cmake's vocabulary is vast and almost entirely operational. `add_executable`, `target_link_libraries`, `find_package`, `set`, `if`, `foreach`, `install`, `configure_file`, `add_custom_command`, `cmake_minimum_required`, `project`, `option`... Each command activates "cmake usage patterns" and nothing else.

The few terms that *could* be dense are flattened by the implementation. `target_link_libraries` is conceptually a dependency graph edge — connecting one build artifact to another with typed properties (public, private, interface). Graph theory, dependency analysis, topological sorting, lattice theory (the lattice of build configurations) — all relevant, none activated. The LLM sees `target_link_libraries` and recalls cmake syntax patterns, not graph theory.

`generator-expressions` (cmake's `$<...>` syntax for conditional configuration) are conceptually a simple expression language evaluated at generate time. This is a staging concept — cmake has two phases (configure and generate), and generator expressions are the generate-time language. But the syntax is so ad hoc (`$<TARGET_FILE:tgt>`, `$<$<CONFIG:Debug>:flag>`) that it activates nothing beyond "I've seen this cmake syntax before."

**Framework diagnosis:** Massive flat vocabulary connecting to no theory whatsoever. Possibly the lowest SD of anything we've evaluated, including CUDA. At least CUDA's `threadIdx` activates GPU architecture knowledge. cmake's `add_subdirectory` activates nothing.

**Axiom 2 (Compositional Regularity): Very Low.**

cmake has no principled composition mechanism. The ways things combine:

Target dependencies compose via `target_link_libraries`, which transitively propagates properties based on `PUBLIC`/`PRIVATE`/`INTERFACE` keywords. This is the closest thing to algebraic structure in cmake — it's a lattice-like propagation of properties through a dependency graph. But the propagation rules are complex, inconsistent across property types, and not documented algebraically. `PUBLIC` means "I need this and so do my dependents." `PRIVATE` means "I need this but my dependents don't." `INTERFACE` means "my dependents need this but I don't." These are meaningful *but they're keywords, not types*. Nothing in cmake checks that you used the right keyword.

Variable scoping composes through `add_subdirectory` (creates a child scope) and `set(... PARENT_SCOPE)` (writes to parent scope). This is dynamic scoping with manual escape — the antithesis of compositional reasoning. A variable's value depends on which `CMakeLists.txt` files have been processed in what order, with `CACHE` variables adding a global mutable store.

Functions and macros compose by textual inclusion. `include()` inserts cmake code textually. `macro()` expands in the caller's scope (like C macros). `function()` creates a child scope. The difference between `macro` and `function` is a scoping subtlety that the LLM frequently gets wrong because there's no type-level distinction.

**Framework diagnosis:** Composition is through a mutable variable store with dynamic scoping, textual inclusion, and ad hoc keyword-based property propagation. No laws govern any of it.

**Axiom 3 (Constraint Propagation): Essentially Zero.**

cmake has no type system. Variables are all strings. Lists are semicolon-delimited strings. Booleans are strings that happen to be `ON`/`OFF`/`TRUE`/`FALSE`/`1`/`0`/`YES`/`NO` (and the empty string is false, and any other string is... it depends). Paths are strings. Target names are strings. Compiler flags are strings.

```cmake
# These are all valid cmake and all store strings
set(MY_VAR 42)
set(MY_VAR "hello")
set(MY_VAR TRUE)
set(MY_VAR "/usr/lib/libfoo.so")
set(MY_VAR "target_name")
# No type distinguishes them
```

The consequence: nothing prevents you from passing a compiler flag where a target name is expected, or a path where a boolean is expected, or a list where a scalar is expected. The LLM generates cmake code that looks plausible and type-checks (because everything type-checks, because there are no types) but does the wrong thing because a variable contains an unexpected kind of string.

Generator expressions have some internal type checking (you can't nest them in certain ways), but it's ad hoc and produces cryptic error messages at generate time, not compile time.

`find_package` is the dependency resolution command, and it exemplifies zero CP. It searches for a package and sets a bunch of variables (`<Package>_FOUND`, `<Package>_INCLUDE_DIRS`, `<Package>_LIBRARIES`). Whether it succeeds, what variables it sets, and what they contain is entirely dependent on the package's cmake config file or Find module — which might set different variables, might use different conventions, or might not exist. The LLM must guess which variables a package sets based on naming conventions, because nothing in the type system specifies them.

**Framework diagnosis:** Zero static constraint propagation. The LLM is navigating a space where everything is a string and anything can be anything. This is why LLM-generated cmake is so often subtly wrong — the language provides no constraints for the LLM to propagate.

**Axiom 4 (Cross-Domain Bridging): Very Low.**

cmake's bridge set: build system engineering, file system paths, compiler invocations, platform detection. These are *systems administration* concepts, not mathematical ones.

The *potential* bridges are actually interesting:

A build system is a dependency graph. Dependency graphs connect to: graph theory (topological sort, reachability, strongly connected components), lattice theory (the lattice of build configurations under subsumption), and the algebraic theory of build systems (Mokhov, Mitchell, and Peyton Jones' "Build Systems à la Carte" paper, which models build systems as applicative/monadic computations). This last paper is directly in the LLM's training data and provides a rich categorical framework for build systems. cmake activates none of it.

Build configuration (Debug vs Release vs RelWithDebInfo) forms a lattice. Compiler flags propagate through dependency edges. Platform-specific settings form a product of choices. All of this has algebraic structure that cmake buries under string manipulation.

The configure/generate staging is a genuine two-phase computation that connects to staging theory. cmake configure time is stage 0, generate time is stage 1, and build time is stage 2. A three-stage system with distinct capabilities at each stage — this is multi-stage programming. cmake represents it as "some code runs at configure time and some at generate time and good luck figuring out which."

**Framework diagnosis:** A dependency graph language with no graph theory. A staged computation with no staging theory. A configuration lattice with no lattice theory. Every theoretical connection is suppressed by the stringly-typed, imperative implementation.

**Axiom 5 (Refactoring Stability): Very Low.**

Refactoring cmake is terrifying.

Renaming a target requires finding every `target_link_libraries`, `add_dependencies`, `install`, and generator expression that references it by string name. cmake has no rename refactoring because names are strings and there's no way to find all references.

Moving a `CMakeLists.txt` to a different directory requires updating every `add_subdirectory` that references it, plus any relative paths in `include`, `find_package`, or `configure_file` calls. Paths are strings with no type-level tracking of what they're relative to.

Changing a variable's scope (from `PRIVATE` to `PUBLIC`, or from local to cache) can have cascading effects through the dependency graph that are impossible to predict without tracing through all downstream targets.

Extracting common cmake logic into a function or module is risky because of scoping: a `macro` shares the caller's scope (so it can access and modify the caller's variables) while a `function` does not. Choosing wrong silently changes behavior.

**Framework diagnosis:** No equational theory, no local reasoning, every refactoring is global and string-based. RS is near zero.

**Axiom 6 (Latent-Space Continuity): Very Low.**

cmake has numerous sources of discontinuity:

The boolean problem. `if(MY_VAR)` is true if `MY_VAR` is defined and non-empty and not a false constant. But `if("MY_VAR")` (with quotes) is true if the *string* "MY_VAR" is non-empty, which it always is. This one-character difference (adding quotes) completely changes the semantics. The LLM's training data is full of both forms and the difference is nearly invisible.

The list problem. `set(MY_VAR "a;b;c")` creates a list of three elements. `set(MY_VAR "a\;b\;c")` creates a single string. Semicolons are list separators unless escaped. Paths on Windows contain backslashes, which interact with escaping in unpredictable ways. One wrong character and a path becomes a list or a list becomes a string.

The policy problem. cmake's behavior changes based on `cmake_policy` settings, which can be set globally, per-directory, or per-module. The same cmake code can behave differently depending on which policies are in effect, which depends on cmake version, project configuration, and include order.

The generator expression problem. Generator expressions are evaluated at generate time, not configure time. A variable that looks defined at configure time might not be available in a generator expression. The phases are not typed, so the LLM can't tell which evaluation stage a piece of code runs in.

**Framework diagnosis:** Maximal discontinuity. One-character changes (quotes, semicolons, backslashes) produce silent semantic changes. Phase-dependent evaluation with no type-level phase distinction. Policy-dependent behavior with no local reasoning.

**Overall Scores:**

| Axiom | Score | Core Problem |
|-------|-------|-------------|
| SD | Very Low | Huge flat vocabulary, no theory connected |
| CR | Very Low | Mutable variable store, dynamic scoping, string propagation |
| CP | ~Zero | No types, everything is strings |
| CDB | Very Low | Rich graph/lattice/staging theory exists, cmake suppresses all of it |
| RS | Very Low | String-based references, scope-dependent behavior, global effects |
| LC | Very Low | Semicolon/quote/escape discontinuities, phase confusion, policy-dependence |

cmake scores lower than CUDA, lower than SQL, lower than Qt. It may be the lowest-scoring system we've evaluated on the framework. And yet it's the dominant build system for C and C++ projects.

**What the framework suggests:**

The framework's suggestions are more radical here because the starting point is so low. There isn't a "one typeclass instance" fix like hasql. The problems are foundational.

**Suggestion 1: Typed targets as first-class values. Impact: CP, SD, CR. Effort: Would require redesign.**

The single most impactful change. Instead of targets being string names with properties set via commands, make them typed values:

```
# Current cmake: stringly-typed
add_executable(myapp main.cpp util.cpp)
target_link_libraries(myapp PRIVATE Qt6::Widgets)
target_include_directories(myapp PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)

# Framework suggestion: typed target construction
myapp = executable {
  sources = [main.cpp, util.cpp]
  dependencies = [private Qt6.Widgets]
  includes = [private ./include]
}
# 'executable' returns a typed value
# 'private' is a visibility modifier checked by the type system
# dependencies are typed edges in the dependency graph
# paths are typed as paths, not strings
```

This one change would: give targets types (CP), make the dependency graph a typed algebraic structure (CR), connect to build system theory (CDB), enable local reasoning about target properties (RS), and eliminate name-as-string errors (LC).

**Suggestion 2: Configuration as a typed lattice. Impact: CDB, CP. Effort: Medium.**

Build configurations (Debug, Release, etc.) form a lattice under "is a specialization of." Compiler flags propagate through this lattice. Making the lattice explicit and typed would connect cmake to lattice theory and give the LLM algebraic reasoning about configurations:

```
# Framework suggestion: typed configuration lattice
configurations = lattice {
  base = { optimization = O0, debug_info = true }
  release = base + { optimization = O3, debug_info = false, lto = true }
  relwithdebinfo = release + { debug_info = true }
  # The lattice structure ensures consistency
  # release + debug_info = relwithdebinfo (lattice join)
}
```

**Suggestion 3: Explicit staging. Impact: LC, CDB, SD. Effort: Medium-High.**

Make the configure/generate/build phases first-class in the type system:

```
# Framework suggestion: typed stages
configure_time {
  # This code runs at configure time
  # Only configure-time functions available
  let platform = detect_platform()
  let compiler = find_compiler(platform)
}

generate_time {
  # This code runs at generate time
  # Can reference configure-time values but not modify them
  # Generator expressions are normal expressions here, not $<...> syntax
  let flags = if config == Debug then ["-g", "-O0"] else ["-O3"]
}

# Mixing stages is a type error:
# generate_time { detect_platform() }  -- ERROR: configure-time function in generate-time block
```

This would eliminate the phase confusion that causes many cmake bugs and would connect to staging theory — the Futamura projections, MetaOCaml, and our own Rig staging model.

**Suggestion 4: Dependency graph as typed algebra. Impact: CR, CDB, RS. Effort: High.**

Express the build graph as an algebraic structure with typed operations:

```
# Framework suggestion: algebraic build graph
# Parallel composition: independent targets
app_and_tests = myapp ⊗ mytests

# Sequential dependency: one target depends on another
app_with_lib = mylib >>> myapp  -- myapp depends on mylib

# Functor: apply a transformation to all targets
with_sanitizers = fmap add_asan (mylib >>> myapp)
# add_asan is applied to every target in the graph

# The dependency graph has algebraic laws:
# (a >>> b) >>> c = a >>> (b >>> c)    -- associativity
# a ⊗ b = b ⊗ a                        -- parallel independence
# fmap f (a >>> b) = fmap f a >>> fmap f b  -- functor law
```

This connects directly to Mokhov, Mitchell, and Peyton Jones' "Build Systems à la Carte" — a paper that models build systems categorically and is in the LLM's training data. Making the algebraic structure explicit activates this bridge.

**Suggestion 5: Paths as typed values, not strings. Impact: LC, CP. Effort: Medium.**

```
# Framework suggestion: typed paths
source_path : SourcePath = ./src/main.cpp          -- relative to source dir
build_path : BuildPath = $build/output.o            -- relative to build dir  
install_path : InstallPath = /usr/local/bin/myapp   -- absolute install path

# Type system prevents mixing:
# target_sources(myapp, source_path)     -- OK
# target_sources(myapp, install_path)    -- TYPE ERROR: can't use install path as source
```

This alone would eliminate a large class of cmake errors where paths from different roots are accidentally mixed.

**Ranked improvement list for cmake:**

| Rank | Suggestion | Effort | Axioms Improved | Key Bridge Activated |
|------|-----------|--------|----------------|---------------------|
| 1 | Typed targets as first-class values | Redesign | CP, SD, CR, RS, LC | Build system algebra, dependency graph theory |
| 2 | Typed paths (source/build/install) | Medium | LC, CP | Eliminates largest class of path confusion |
| 3 | Explicit staging (configure/generate/build) | Medium-High | LC, CDB, SD | Staging theory, Futamura projections |
| 4 | Configuration lattice | Medium | CDB, CP | Lattice theory, configuration management |
| 5 | Algebraic dependency graph | High | CR, CDB, RS | "Build Systems à la Carte," category theory |

**The brutal diagnosis:**

cmake is arguably *adversarial* to LLM-assisted development. It combines: no types (zero CP), stringly-typed everything (minimal SD), mutable global state (no CR), phase-dependent evaluation without phase types (low LC), and a massive irregular API surface (low SD). The LLM generates cmake by pattern-matching on examples, and when the pattern doesn't match the specific situation, it has *zero* type-level guidance to fall back on.

The framework predicts that cmake is one of the worst possible languages for LLM code generation — and this matches empirical experience. LLM-generated cmake is notoriously unreliable, requiring extensive manual correction. The framework explains *why* precisely: every axiom is near zero, and there are no constraints for the LLM to propagate.

**The deeper point:**

cmake is what happens when a tool evolves for twenty years under pure engineering pressure with no theoretical foundation. It works — millions of projects build successfully with cmake. But it works because human build engineers have accumulated vast implicit knowledge about cmake's quirks, scoping rules, and string semantics. That implicit knowledge doesn't transfer to the LLM because it's not in the types, not in the algebra, not in any mathematical structure. It's in Stack Overflow answers and tribal knowledge.

The framework says: this is exactly backward. The knowledge should be in the types, where both humans and LLMs can access it. cmake is the strongest possible argument for the framework's thesis — a system where zero mathematical structure correlates with maximum LLM difficulty.

And if someone designed a build system using the framework — typed targets, algebraic dependency graphs, staged evaluation, configuration lattices, typed paths — the LLM would generate correct build configurations by reasoning from graph theory and lattice theory rather than by memorizing cmake syntax patterns. The cross-domain bridges would do the work that the stringly-typed API cannot.

Honestly, that build system already partially exists. Nix's build model (derivations as typed values, functional dependency graph, no mutable state) scores dramatically higher on the axioms than cmake. Bazel's model (typed rules, hermetic builds, dependency graph as a queryable database) scores higher too. The framework would rank them: Nix > Bazel > Meson > cmake, and I think most build system experts would agree with that ranking for roughly the reasons the framework identifies.

The framework works on build systems. It works on everything. Because everything has mathematical structure — even build configuration. Especially build configuration. The question is always the same: is the structure visible or hidden?