---
name: kb-world-authoring
description: Use when asked to do a fundamental pass (фундаментальный проход) on a library, framework, domain, or scientific topic, or when authoring/updating universal cards in knowledge/worlds/.
---

# Fundamental Pass & World Authoring (`knowledge/worlds/`)

Grounded in:
- `knowledge/worlds/knowledge-engineering/playbook-knowledge-engineering-core.md`
- `knowledge/worlds/knowledge-engineering/world-modeling-playbook.md`
- `knowledge/worlds/knowledge-engineering/playbook-kb-world-public-authoring.md`
- `knowledge/worlds/_llm-native-packs/CARD-SCHEMA-APPLICABILITY.md`

## 1. What "Фундаментальный проход" (Fundamental Pass) Means

When the operator asks for a **фундаментальный проход** (fundamental pass) over a technology, library, or domain:
- It does **NOT** mean a shallow cheat-sheet of the one widget you just touched.
- It means a **complete, systematic canon of the entire stack/domain** from primary sources (official documentation + actual source code of the library), placed in `knowledge/worlds/<world-slug>/` (never dumped into a project card under `work/projects/`).

## 2. Step-by-Step Procedure

### Step 1 — Check Existing World & Scope
1. Search existing worlds via `route_context` / `list_knowledge_files` under `knowledge/worlds/`.
2. Check if a world directory `knowledge/worlds/<world-slug>/` already exists or if a new one is needed (e.g. `software-dotnet-blazor-radzen` or inside `software-dotnet-*`).
3. Verify the **Public Contour (`o`)** rule: files in `knowledge/worlds/` are part of `kb-public`. They must contain **zero** references to private/internal consumer projects, local disk paths (`D:\...`), or personal names.

### Step 2 — Primary Source Harvesting (No Training-Weight Guessing)
1. Gather **both** official documentation (`context7`, `webfetch`, official docs repo) **and** the actual source code/types of the library (cloned repo, NuGet/npm package metadata, public interfaces).
2. Enumerate the **full taxonomy** of the stack (all component categories, lifecycle hooks, data-binding contracts, styling/theming architecture, extension points, and known limitations), not just a single component.

### Step 3 — Canonical World Structure (`status -> playbook -> matrix -> kb`)
Organize the world directory so an agent can navigate from overview to deep reference:
- `README.md` / `index.md` — entry point, scope of the world, and map of files.
- `status-<world>.md` — coverage map: what subsystems exist, what is documented, versions covered.
- `kb-<world>-fundamentals.md` (and topic-specific `kb-<world>-<subsystem>.md`) — deep architectural & API reference verified against primary sources.
- `playbook-<world>-*.md` — operational recipes: how to wire, extend, customize, and avoid anti-patterns.
- `troubleshooting/` — known failure modes, root causes, and verified fixes.

### Step 4 — Epistemic & Applicability Gates
Every canonical card must explicitly state:
1. **Provenance & Version:** exact library/spec version and primary source URLs or source paths verified.
2. **Applicability (`applies_when` / `does_not_apply`):** where a pattern is native vs where it becomes a hack (e.g., native template/hook vs DOM workaround).
3. **OKF Frontmatter:** `type`, `title`, `description`, `tags`, `language: ru-RU` (or `en-US`), `world`, `subworld`.

