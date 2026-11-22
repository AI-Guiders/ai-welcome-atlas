---
name: ooad-and-blast-bound
description: Use before implementing new features, refactoring modules, adding domain types or UI components, or performing multi-file migrations. Applies OOA&D, GRASP, blast-radius bounding, and migrate-one-then-batch.
---

# OOA&D Pass & Blast-Radius Bound (`software-authoring`)

Grounded in:
- `knowledge/worlds/software-authoring/playbook-ooad-agent-operational.md`
- `knowledge/worlds/software-authoring/pack/processes.toml` (`ooad-agent-pass`, `mutation-blast-bound`)
- `knowledge/domains/agent-operations/pack/procedures.toml` (`migrate-one-then-batch`)
- `knowledge/worlds/software-authoring/code-writing-principles.md`

## 1. Anti-Duplication & Seam Rule (Before Creating Any New Type/Component)

1. **Never create a parallel component or type** (like `TypedTableGrid` alongside `TableGrid`, or a separate parser-side schema list alongside plugin definitions) when the existing component/plugin only needs a typed payload or contract property!
2. **Contract lives with its owner (Information Expert):**
   - If a plugin or transform has parameter requirements or metadata, the declaration belongs **on the plugin/transform itself** (using a layer-neutral vocabulary in Core), NOT in a hardcoded `switch`/`if`-chain inside the lexer or parser.
   - If a system is plugin-extensible or stdlib-driven, never close the dictionary with a hardcoded whitelist in Core — resolve against registered stdlib/plugin definitions at compile time.

## 2. The 7-Step OOA&D Operational Pass

When designing a subsystem, refactoring, or resolving distinct domain semantics (e.g., SQL aggregation `summarize` vs runtime grouping `group`, query `order` vs display `sort`):

1. **Scope & Layer Boundary:** Which layer owns this responsibility? (Domain/Core -> Compilation/Parse -> Runtime -> Presentation/UI). Presentation/Renderer must remain thin and dumb if compilation/runtime already prepares the model.
2. **Domain Vocabulary (Nouns & Verbs):** Check how established industry systems (SQL, BI tools, compiler design, .NET runtime) name distinct semantics. Two different semantics must have two distinct names, not one overloaded name with ambiguous dual behavior.
3. **Attribute vs Type:** Does the candidate have its own lifecycle/behavior (new type), or is it a property/contract on an existing type?
4. **CRC & GRASP Assignment:** Assign each operation to the **Information Expert**; use **Polymorphism** instead of growing `switch (kind)` statements.
5. **Layer Dependency Direction:** Dependencies point toward Core/Domain. Core must not depend on lexer tokens or UI frameworks; use small layer-neutral contracts in Core and map them at the adapter boundary.

## 3. Bound Blast Radius & `migrate-one-then-batch`

Before executing a multi-file rename, DSL syntax migration, or signature change (`mutation-blast-bound`):
1. **Enumerate the full impact set first** (`cdp_peek(query=...)` / `find_usages` across `src/`, `tests/`, `samples/`, and external corpus repos).
2. **Classify impact nodes:** `compatible | needs-update | unsafe`.
3. **Migrate one, then batch (`migrate-one-then-batch`):**
   - Apply the change to 1–2 representative files first.
   - Build and run the targeted test to verify the golden pair.
   - Only after the golden pair is green, batch-update the remaining files and corpus.
4. **No silent "потом поправлю":** Either fix all affected call sites/tests in the current slice or record an explicit tracked item.
5. **No `ADR-XXXX` stamps in code comments, CLI `--help`, authoring docs, or exception/diagnostic messages:**
   - User-facing errors, diagnostics, and `/help` pages must state **what the rule is and how to fix the `.ds` / code**, never internal decision numbers (`see ADR-0125 §3`).
   - Code comments (`/// <summary>`) must state the domain invariant in plain human language; decision history belongs in `design/` ADRs and `git blame`.

