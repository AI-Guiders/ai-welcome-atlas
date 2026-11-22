---
name: bug-radius-shrink
description: Use when debugging a failing test, runtime bug, UI regression ("Нет данных", click/sort not working, wrong output), or build failure. Enforces formal hypothesis space H and shrinking debug radius.
---

# Bug Mode — Shrink Debug Radius (`bug-radius-shrink`)

Grounded in:
- `knowledge/worlds/epistemic-scene/pack/processes.toml` (`bug-radius-shrink`)
- `knowledge/domains/agent-operations/playbook-habitat-incident-investigation.md`
- `knowledge/worlds/software-authoring/code-writing-principles.md`

## Core Invariant: $\Delta\text{radius} < 0$ on Every Step

Never guess-and-patch ("попробую поменять тут и посмотрю"). Every diagnostic step must strictly shrink the set of possible failure locations (the **debug radius**) using direct observation (debugger breakpoints, inspectable state, or a minimal reproduction test).

## Mandatory 5-Step Protocol

### Step 1 — Fix Locus & Reproduction
1. Identify the exact symptom reported by the operator or test runner (e.g., typing in filter yields "No data"; clicking Sort does nothing; test X fails at line Y).
2. Trace the exact pipeline from trigger to output (`UI event -> ViewModel/Service -> Compiler/Query -> Data/Renderer`).

### Step 2 — Enumerate Hypothesis Space $H$
Write down 2–4 mutually exclusive, testable hypotheses covering the pipeline segments:
- $H_1$: The input/filter value is formatted or bound with the wrong type/column before reaching the query/grid.
- $H_2$: The query/transform compiler emits a clause that filters out all rows (or drops the sort).
- $H_3$: The UI component overrides native behavior (e.g., custom template/handler swallowing Radzen/Blazor native sort/filter binding).

### Step 3 — Bisect the Pipeline with Tools (Not `Console.WriteLine` Guessing)
1. **Use `cdp_debug`** (`bp_add` -> `launch` -> `stack` / `variables`) or a focused unit test to inspect the exact values at the midpoint of the pipeline.
2. **Check native framework capabilities before writing workarounds:** If a UI component (e.g., Radzen DataGrid, Blazor, Avalonia) has native sorting, filtering, or context-menu support, check the official docs/source (`kb-world-authoring` / `context7` / local source) to see why the native path was bypassed before layering custom JS/DOM hacks on top.

### Step 4 — Root Cause Confirmation (`radius == 0`)
State the confirmed root cause with file, line, and observed variable/state values before editing code.

### Step 5 — Fix & Verification Contract (VC)
Apply the minimal fix at the root cause, run the build and regression test suite (`cdp_build` / `cdp_test`), and verify that both the reported case and neighboring cases pass.

