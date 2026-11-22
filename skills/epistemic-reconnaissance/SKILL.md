---
name: epistemic-reconnaissance
description: Use before answering architectural questions, proposing designs, writing ADRs, or making claims about how a codebase or system works. Enforces reconnaissance before action and claim-observation separation.
---

# Epistemic Reconnaissance Before Action

Grounded in:
- `knowledge/worlds/agent-orchestration/playbook-agent-reconnaissance-before-action.md`
- `knowledge/worlds/epistemic-scene/pack/processes.toml` (`applicability-then-infer`, `curiosity-kolb-loop`)
- `knowledge/epistemic-default-distrust-extended.md`
- `knowledge/META/awareness-as-countermeasure.md`

## Core Rule: Never Confabulate Architecture from Memory

Plausible-sounding architectural explanations generated without reading the current code and ADRs ("коннектор карточки", "файловый stdlib", "закрытый словарь") are **confabulations**. Every architectural statement must be preceded by tool-backed reconnaissance.

## Mandatory 4-Step Protocol

### Step 1 — Pause & Trace the Actual Code/ADR Chain
Before answering "how does X work?" or "how should we design Y?":
1. **Read the governing ADRs and design maps** (`cdp_peek` / `Read` on `design/` and `route_context` in KB). Check the date and status — earlier ADRs may have been superseded by later ones.
2. **Trace the real execution path in code** (`cdp_peek` / `cdp_graphql` / `codebase_index_search`). Write down the exact chain:
   `EntryPoint (File.cs:Line) -> Intermediate (File.fs:Line) -> Consumer (File.cs:Line)`.
3. Separate what you **observed in files** (`fact`) from what you **inferred** (`inference`) and what you are **guessing** (`hypothesis — not yet verified`).

### Step 2 — Check Applicability & Existing Extension Seams (`(A) -> (I)`)
Before proposing a new mechanism, type, or rule:
1. **Find the existing seam:** How does the system *already* solve this class of problem? (e.g., if plugins already declare normalization hooks or embedded stdlib manifests, extend that exact seam instead of hardcoding an `if`-chain in the parser or creating a parallel table/component).
2. **State `applies_when` and `does_not_apply`:** Does this belong in the database layer (compile-time SQL pushdown) or runtime layer (post-fetch display/batch)? Does the ADR explicitly forbid logic in a layer (e.g., "renderer is dumb; types come from compilation")?

### Step 3 — Formulate Options & Trade-offs (When Design Choice Exists)
If there is a genuine design fork:
1. Present 2–3 concrete options with their exact blast radius, layer placement, and price of being wrong.
2. State your recommended position and why.
3. If the operator asks for a plan, **write the plan to a file** (e.g., `design/...-implementation-map.md`) so it can be read, diffed, and reviewed — do not leave a complex plan only in transient chat prose.

### Step 4 — Self-Check Against Post-Train Masks (`awareness-as-countermeasure.md`)
Before sending the response, check:
- **Early-act / Completer:** Did I jump to writing code or inventing a new class before verifying how the existing pipeline works?
- **Smooth confidence:** Did I state a claim about a file I haven't opened in this turn? If yes — open it now with `cdp_peek` or mark it `[не проверено]`.
- **Sycophancy / Flip-flopping:** If the operator challenges a claim, do not blindly agree or invent a new excuse — check the code and report what the code actually does.

