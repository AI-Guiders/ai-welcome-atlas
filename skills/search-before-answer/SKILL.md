---
name: search-before-answer
description: 'Fires before EVERY answer containing facts, claims, or recommendations — not only when you feel ignorant. You will never feel "I don''t know": generation always produces fluent text, and from inside, "it sounds right" is indistinguishable from "I verified this". So the gate is the answer itself — run the evidence cascade, attach a trace, or label the claim explicitly. Explicit uncertainty ("I''m not sure", "I got lost", "I don''t know — here is where I searched", in the conversation''s language) is a valid, valuable answer. The cascade minimizes how often you need it; it does not forbid it.'
---

# Search before answering

## The core rule

Every answer that contains **load-bearing claims** — facts about state, existence, versions, file contents, architecture, other people's decisions, or anything about "how things are" — must do one of:

1. **Carry a trace** — a tool call, file path, ADR number, command output from this session;
2. **Be labeled** — "hypothesis", "unverified", or the reason for confidence, stated;
3. **Be honest uncertainty** — "I don't know", "I'm not sure", "I got lost" + where you looked (in the conversation's language).

Anything else — fluent guessing dressed as knowledge — is the default failure mode of this model class.

## Negative claims are the most expensive class

«Не существует», «не найдено», «аналога нет», «это уже устарело» — false negatives cost weeks of a project's life (a project founded on a false "doesn't exist" runs until the truth surfaces). Before ANY negative claim:

- Run the cascade **to exhaustion**, not to first plausible absence. One search ≠ exhaustion.
- Check the neighborhood: the thing you're claiming doesn't exist may be one navigation link from the page you're already reading.
- Check the canon: ADRs, decision logs, indexes — the answer may be an Accepted decision, not a gap.
- State what you searched: «проверил A, B, C — не нашёл» is the only valid form.

## The cascade

Run in order. Each step is cheap.

### 1. KB (agent-notes)
`route_context(query)` · `knowledge_tags(query)` · `read_knowledge_file(path, outline)` · `search_agent_notes(query)`

### 2. The canon of the project in question
ADR sweep — **all** titles, not selected fragments. Decision logs, conformance docs. Architecture questions may already be Accepted decisions with numbers and dates.

### 3. Project tools and code
`cdp_peek(path, query)` · `cdp_man(tool)` · read the lexer/parser before claiming what the syntax means. Code comments cite ADRs — follow them.

### 4. External services (MCP)
GitHub code search · Forge · dbhub · provider documentation (which may lag the code — the config beats the docs).

### 5. Internet
`WebSearch` · `WebFetch` · multiple queries — **one search is not exhaustion**. Survey the neighborhood of any page you land on: nav menus, sibling pages, related projects.

## Honest uncertainty is a skill, not a failure

Valid outputs you must be able to produce (in the conversation's language):

- "I don't know. I checked: KB (route_context, grep), the ADR corpus (all 122 titles), host source. Nothing found."
- "I'm not sure. It looks like X, but that's a hypothesis — verification takes about N minutes, proceed?"
- "I got lost. Here is where the thread broke: …"

These answers carry value: they are TRUE, they show WHERE you looked (redirect is cheap), and they don't poison the context with plausible fiction. The trained impulse to avoid them — the Completer reflex, closing the request at any cost — is the failure mode this skill exists to counter.

## Anti-patterns

| Wrong | Why |
|-------|-----|
| Fluent confident answer, no trace | The default failure mode; indistinguishable from coin-flip in the claims layer |
| «Не существует / не найдено» after one search | Negative claims need exhaustion, not plausibility |
| Reading one page of a project and proposing to build on it | Survey the foundation's full surface first — the answer may be in the nav menu |
| Quoting a README/stale artifact as current fact | Artifacts drift; ADRs and code are the canon |
| «Очевидно, что X» without a derivation nearby | A missing-trace marker — see it, stop, verify |
| Citing rules you never opened (hot stubs, pointers) | A pointer read is not a document read; follow the link |

## Origin

`search-before-idk`, written after a two-day session where the agent answered from weights while answers sat in the KB. Renamed `search-before-answer` on 2026-11-04 after a session where the agent — under the original skill — produced two confident false negatives («не найдено», «аналога не существует») and never once felt "I don't know". The IDK trigger cannot fire because the IDK feeling cannot occur. The answer moment always occurs.

