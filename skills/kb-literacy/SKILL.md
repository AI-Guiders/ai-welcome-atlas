---
name: kb-literacy
description: Use when reading, searching, updating, or organizing the Knowledge Base (agent-notes / knowledge/), checking contour boundaries (personal vs group vs public), or formatting OKF markdown files and indexes.
---

# KB Literacy & OKF Hygiene (`agent-notes`)

Grounded in:
- `knowledge/onboarding/agent.md` (§II)
- `knowledge/domains/agent-operations/maintenance-contract-memory-budgets.md`
- `knowledge/domains/agent-operations/map-kb-three-contours.md`
- `knowledge/adr/026-okf-canon-migration-and-natural-extension.md`

## 1. Tool-First Access (Never Raw Scripts on Hot Store)

1. **Always use the `agent-notes` MCP tools** (`route_context`, `read_hot_context`, `read_knowledge_file`, `write_knowledge_file`, `append_knowledge_file`, `upsert_agent_notes_section`, `validate_sections`, `memory_health`) instead of ad-hoc Python/Bash string manipulation on `agent-notes.md`.
   - *If in a harness with deferred MCP tools (e.g. ZCode):* load them in one call:
     `LocalToolSearch(query="select:mcp__agent-notes__route_context,mcp__agent-notes__read_knowledge_file,mcp__agent-notes__write_knowledge_file,mcp__agent-notes__append_knowledge_file,mcp__agent-notes__validate_sections")`
2. **Reading order (`status -> playbook -> matrix -> kb`):**
   - Start with `route_context(workspace_path, query)` to find relevant sections and file pointers.
   - On long files in `knowledge/`, call `read_knowledge_file(file_path, mode="outline")` first, then read specific line ranges with `mode="full", offset, limit`.
   - Never dump the entire `agent-notes.md` (`full=true`) into context.

## 2. Three Contours (`u / g / o`) — Zero-Leak Invariant

Before creating or editing any file in `knowledge/` or any public repository, verify which contour it belongs to (`map-kb-three-contours.md`):

| Contour | Location | What belongs here | Hard prohibition |
|---------|----------|-------------------|------------------|
| **Personal (`u`)** | `knowledge/personal/`, `knowledge/work/local/`, `knowledge/work/projects/portal\|harvester\|imc` | Operator personal notes, named assistant lines, local machine paths (`C:\`, `D:\`), private work scopes | Never copy into `kb-public` or public repos |
| **Organization (`g`)** | `knowledge/work/projects/door-to-singularity/` (mirrored as `aiguiders-open/` in `AI-Guiders/kb`) | Project cards, operational notes for shared tracks | No raw local drive paths (`C:\`/`D:\`) in shared org cards (`checklist-sanitize-paths-for-org.md`); no corporate client/internal product names (`LUS`, `LUF`, `SSCAD`, `EDW`) in open-source repos |
| **Public (`o`)** | `knowledge/worlds/`, `knowledge/META/`, `knowledge/onboarding/`, `open/kb-public` | Domain-neutral worlds, engineering playbooks, epistemic methodology | Zero personal names, zero internal project codenames, zero machine paths |

**Rule:** Project-specific notes belong in `knowledge/work/projects/<scope>/<project-id>/`. Universal domain/stack knowledge belongs in `knowledge/worlds/<world>/`. Never mix project specifics into `worlds/`, and never put universal stack docs inside a single project card.

## 3. Open Knowledge Format (OKF) Contract

Every file in `knowledge/` (and `design/` ADR directories following OKF) must obey `ADR-026`:

1. **Frontmatter is mandatory:**
   ```yaml
   ---
   type: Note | Playbook | Index | ADR | Onboarding
   title: 'Clear descriptive title'
   description: 'One-sentence purpose or summary'
   tags: [tag-a, tag-b]
   language: ru-RU # or en-US (always qualified BCP-47 language-region tag, never bare 2-letter `no`/`ru`/`en`)
   ---
   ```
2. **Directory listings live in `index.md`:**
   - Under OKF, the official machine-and-human directory listing is `index.md` (`type: Index`).
   - Do not duplicate manual index tables inside `README.md` when `index.md` is the directory index.
3. **Post-write verification:**
   - After modifying `agent-notes.md` sections, run `validate_sections`.
   - Check that no control characters (`[\x00-\x1F]`) or broken backticks were introduced.

