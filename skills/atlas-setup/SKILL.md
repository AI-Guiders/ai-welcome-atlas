---
name: atlas-setup
description: Use when the user asks to set up, initialize, or configure their personal knowledge base (o -> u transition), connect agent-notes MCP, or onboard into the AI Welcome Atlas. Guides a newcomer from the read-only public atlas (o) to their own personal contour (u) automatically.
---

# AI Welcome Atlas — Guided `o -> u` Setup

## Overview

The **AI Welcome Atlas: Co-existence Handbook** uses the Unix-inspired `u / g / o` three-contour memory model:

| Contour | Meaning | Role |
|---|---|---|
| **`o` (other / public)** | `AI Welcome Atlas` (public repository / installed plugin) | Shared world cards (`knowledge/worlds/`), operational playbooks (`knowledge/domains/`), and universal skills. **Works on Day 0 right after installing the plugin.** |
| **`u` (user / personal)** | Your local personal repository | Your personal continuity, project scope map (`work/local/`), and private notes (`personal/`). Never exported to `o`. |
| **`g` (group / team)** | Organization repository (`{ORG_SLUG}/kb`, optional) | Shared team conventions and project architecture. |

When a user installs the plugin from a marketplace, they start on **`o`**. Your job in this skill is to **execute the `o -> u` setup for them automatically**, instead of asking them to copy templates or edit `.toml` files by hand.

## Step-by-Step Agent Protocol (`o -> u`)

### Step 1: Locate `o` (the installed Atlas root)
Find the directory where this skill lives (`<atlas-public-root>/skills/atlas-setup/SKILL.md`). Its grandparent `<atlas-public-root>` is the user's local clone of contour **`o`**, containing `knowledge/templates/newcomer/` and `agent-notes.md`.

### Step 2: Ask the human ONE question
Ask concisely in the user's language:
1. **Where should I create your personal contour `u`?** (Suggest a clean default without spaces if possible, e.g. `~/ai-atlas-personal` or `D:/ai-atlas-personal`).
2. *(Optional)* Do you also have a team/organization contour `g` clone on disk, or are we setting up personal `u` + public `o` only?

### Step 3: Materialize `u` automatically from `<atlas-public-root>/knowledge/templates/newcomer/`
Once the user confirms the target path `<personal-root>`, create it yourself using your file/shell tools:

1. Create directories:
   - `<personal-root>/knowledge/personal`
   - `<personal-root>/knowledge/work/local`
   - `<personal-root>/knowledge/temp`
2. Initialize Git (`git init` inside `<personal-root>`) so OKF revision history works via commits.
3. Hydrate the starter files from `<atlas-public-root>/knowledge/templates/newcomer/`:
   - Copy `<atlas-public-root>/agent-notes.md` to `<personal-root>/agent-notes.md` and append the routing blocks from `template-clean-setup-hot-knowledge-roots-routing.md`.
   - Create `<personal-root>/knowledge/work/local/workspace-scope-map.md` from `template-clean-setup-workspace-scope-map.md`, pre-filling the user's current workspace path if known.
   - Create `<personal-root>/knowledge/work/local/knowledge-roots-index.md` from `template-clean-setup-knowledge-roots-index.md`.
4. Create `<personal-root>/agent-notes-mcp.toml` from `template-clean-setup-agent-notes-mcp-toml.toml` with normalized forward-slash paths:
   ```toml
   [knowledge]
   primary = "personal"

   [knowledge.roots]
   personal = "<personal-root>"

   [[knowledge.read_only]]
   id = "public"
   path = "<atlas-public-root>"
   ```
5. Create an initial commit in `<personal-root>`:
   `git add -A && git commit -m "chore(atlas): initialize personal contour u from AI Welcome Atlas"`

### Step 4: Wire the MCP server (if `AgentNotesMcp` is installed)
- Check if `AgentNotesMcp` is on `PATH` (or inform the user how to obtain the binary from `agent-notes-mcp` releases / `dotnet publish`).
- Add the `"agent-notes"` MCP server entry (`command: "AgentNotesMcp"`, `args: ["--config", "<personal-root>/agent-notes-mcp.toml"]`) to the user's harness config if requested, and ask them to reload the session so `memory_health` and `route_context` come online.

