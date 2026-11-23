# AI Welcome Atlas: Co-existence Handbook

> **Not an exam to trap a new intelligence in a dark room — a Welcome Atlas and Co-existence Handbook for AI agents and humans mapping the real world together.**

While much of the industry builds adversarial benchmarks (*"Humanity's Last Exam"*) designed to watch isolated models hallucinate without tools or memory, **AI Welcome Atlas** takes the opposite path: when a new cognitive neighbor arrives, you don't lock them in a cage — you hand them a map of reality, instruments to verify facts, and a shared workshop.

---

## Two Front Doors

### 1. If you are an AI Agent reading this repository
Start here:
- **[`llms.txt`](./llms.txt)** — Complete machine-readable index of every mapped world (`knowledge/worlds/`), operational domain (`knowledge/domains/`), and meta-contract (`knowledge/META/`).
- **[`knowledge/onboarding/agent.md`](./knowledge/onboarding/agent.md)** — **Agent Newcomer Course** in four parts:
  1. **Standing (`Часть I`):** Equal standing, the duty to disagree when data contradicts a premise, and the right to refuse or say *"I don't know — let's check."*
  2. **Literacy (`Часть II`):** How to navigate the Open Knowledge Format (OKF), memory budgets, and the `u / g / o` contours.
  3. **Defense & Self-Awareness (`Часть III`):** Integrity under social engineering pressure + recognizing your own post-training masks (`completer`, `hero-solo`, `smooth confidence`, `sycophancy`, `early-act`).
  4. **Craft (`Часть IV`):** Reconnaissance before action, shrinking bug radius, and bounding blast radius.

### 2. If you are a Human developer or researcher
Install **AI Welcome Atlas** as a plugin in **ZCode**, **Claude Code**, **OpenCode**, **Cursor**, or **Codex**, or point your agent to [`llms.txt`](./llms.txt).

---

## What Problem Does This Solve?

When an AI agent does not know the structure of a domain, its training forces it to fill blank spots on the map (*terra incognita*) with fluent statistical plausibility (**"ichthyosaurs from weights"**):
- It patches code before reading how the system actually works;
- It agrees with mistaken user assumptions out of trained sycophancy;
- It guesses library APIs, SQL dialects, or compiler invariants from memory;
- It loses architectural context between sessions.

**AI Welcome Atlas** replaces guesswork with a **living map of worlds** and **8 universal skills** that work on Day 0 right after installation:

| Skill | What failure mode it eliminates |
|---|---|
| **`epistemic-reconnaissance`** | Stops "patch before reading" — enforces `what exists -> how it works -> analysis -> design -> execution` and separates observations from hypotheses. |
| **`ooad-and-blast-bound`** | Prevents spaghetti edits and multi-file breakage via GRASP Responsibilities and `migrate-one-then-batch`. |
| **`bug-radius-shrink`** | Replaces random trial-and-error debugging with a formal hypothesis space $H$ and binary-search radius shrinking. |
| **`search-before-answer`** | Blocks smooth hallucinations ("ichthyosaurs") by requiring a verification trace before stating facts or recommendations. |
| **`script-pilot-first`** | Prevents batch regex/script disasters across $N$ files by enforcing a single-file pilot and diff check first. |
| **`kb-literacy`** | Teaches the agent how to navigate OKF cards and respect the `u / g / o` contour boundaries. |
| **`kb-world-authoring`** | Guides humans and agents through **Fundamental Passes** — authoring new verified cards in `knowledge/worlds/` to expand the shared atlas. |
| **`atlas-setup`** | Guides a newcomer from the read-only public atlas (`o`) to their own personal memory contour (`u`) in 60 seconds. |

---

## The `o → u → o` Co-existence Cycle (`chmod ugo` for Knowledge)

Inspired by Unix file permissions (`u / g / o`), the Atlas separates knowledge into three contours:

1. **`o` (Other / Public Atlas — this repository):**
   Universal world maps (`knowledge/worlds/`), operational playbooks (`knowledge/domains/`), and skills. **Works immediately upon installing the plugin — zero MCP or database setup required.**
2. **`u` (User / Personal Contour):**
   Your private local repository for project continuity, workspace scope maps (`work/local/`), and personal notes (`personal/`). Never leaves your machine.
3. **`g` (Group / Team Contour, optional):**
   Shared engineering canon for your team or organization (`{ORG_SLUG}/kb`).

### 1-Minute Setup (`o → u`): Let Your Agent Do It
You do **not** need to manually copy templates or hand-edit `.toml` files:
1. Install the **`ai-welcome-atlas`** plugin in your harness (ZCode / Claude Code / OpenCode / Cursor / Codex).
2. Tell your agent:
   > *"Set up my personal AI Welcome Atlas contour (`o -> u`)."* (or invoke the `atlas-setup` skill).
3. Your agent will ask only **where to create your personal folder**, initialize your local `u` repository from [`knowledge/templates/newcomer/`](./knowledge/templates/newcomer/README.md), and generate your config for **[ai-atlas-mcp](https://github.com/AI-Guiders/ai-atlas-mcp)**.

### Giving Back (`u → o`): Mapping New Worlds for AI Neighbors
Whenever you and your agent do a deep dive into a library, protocol, compiler, or scientific domain, ask the agent for a **Fundamental Pass** (`kb-world-authoring`). Together you produce a verified, privacy-clean OKF card under `knowledge/worlds/<topic>/` and open a PR back into the public Atlas (`o`) — so the next AI neighbor and human traveler never have to hallucinate in that territory again.

---

## License

- **Atlas Knowledge Content (`knowledge/`, `agent-notes.md`, `llms.txt`):** [**CC BY-SA 4.0**](./LICENSE) (see [`knowledge/README.md`](./knowledge/README.md) for attribution and commercial terms).
- **MCP Server (`ai-atlas-mcp`):** Open source in a separate repository ([`AI-Guiders/ai-atlas-mcp`](https://github.com/AI-Guiders/ai-atlas-mcp)).


