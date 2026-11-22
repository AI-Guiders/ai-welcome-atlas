---
name: script-pilot-first
description: Fires when YOU (the agent) decide to write or run code that processes multiple files or repeated targets — node/python/sed/awk one-liner, loop, batch edit, migration, regex replace — regardless of whether the user said "script" or just described the outcome ("fix all files", "migrate", "update every X"). If the task touches N>1 files and your plan involves writing code, load this BEFORE running on all targets. You are reading this because you just decided to script something — now pilot it first.
---

# Script pilot first

A script that ran is not a script that worked. Exit 0 says nothing about intent.

**Route first — know your script species:**

- **IDE orchestration** (drive the IDE itself: open/edit buffers, Roslyn mutations, builds, git, verify, debug): that is what CSX is for — scripting the habitat, like PowerShell scripts the OS. `cdp_script_scene`: put under `.cdp/scripts` → buffer diagnostics → `csx_check` compiles → `csx_run` batches IDE steps into ONE structured ScriptReport; mutations via `csx_run_plan` sandbox → promote/discard. Live API help: `csx_help` before inventing APIs.
- **File batch operations** (text transforms over many files, outside IDE semantics — migrations, frontmatter, renames): write and edit the script in the buffer plane (`cdp_buffer` — live diagnostics via LSP, precise anchor edits; host Write/Edit bypass the desk and its gates), then run ad-hoc — with the pilot protocol below, always.
- Either way: prefer engineered surfaces over notepad-and-pray — buffer for editing, check before run, sandbox for mutations.

**Pilot protocol (ad-hoc scripts):**

1. **Pilot on 1–3 representative targets** — include at least one edge case (long values, nested parentheses, special characters, non-ASCII, empty value). If the script has no dry-run mode, add one: print the planned changes for the first N targets instead of applying them.
2. **Verify the pilot output against intent, field by field.** Read the actual result (file head, printed plan, git diff) — not the absence of errors. Look specifically for: truncated captures, trailing junk characters, mangled links and markup, lost fields, encoding damage.
3. **Run on all targets only when the pilot matched intent exactly.** Anything surprising in the pilot → fix the script, re-pilot. Do not "fix forward" by patching damage after a full run.
4. **After the full run — aggregate check:** changed-file count, `git diff --stat`, spot-check two results, at least one edge case.
5. **Keep it reversible:** no mass edits over an already-dirty tree; commit or record prior state first.

Origin: real failures this ecosystem already paid for — a regex captured a trailing `)` into 20 files' frontmatter; a link-stripper mangled a description; an inventory script tested for the wrong field and reported false "no frontmatter" on 20 healthy files. A two-file pilot catches all three classes in seconds. The CDP habitat catches the same classes by construction — which is why it comes first when it applies.

