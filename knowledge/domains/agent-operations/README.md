---
type: Hub
title: 'Domain: agent-operations'
description: 'Сквозные **операционные контракты агента** (маркеры чата, целостность, параллельные записи в заметки). Не путать с:'
tags: [agent-operations, domains, readme]
language: ru-RU
---
# Domain: agent-operations


Сквозные **операционные контракты агента** (маркеры чата, целостность, параллельные записи в заметки). Не путать с:

- **`META/`** — well-known POST, `integrity-core`, спеки доверия;
- **`worlds/workspace-context/`** — мультипроектный контур, карточки, runbook'и (длинный плейбук);
- **`worlds/agent-orchestration/`** — капитан, параллельные агенты, метрики ускорения.

Имена файлов: **`playbook-*.md`** (`rename-plan.md`). **`section_id`** в `agent-notes.md` сохраняются; stub указывает сюда.

**Полный список концептов:** [index.md](index.md) — генерируется `scripts/build-domain-index.ps1` из frontmatter каталога (OKF v0.2 §8), руками не править. Состав домена отражается в frontmatter файлов (`type`, `title`, `description`), не в этом README.

**Правило приёма (2026-10-28):** домен отвечает «о чём», контур — «для кого». Здесь лежат только **сквозные контракты, протоколы и карты агента**. Проектное (CDP/ZCode/Cursor/OpenCode-специфика), work-юниты, зеркала ADR продуктов — в `work/projects/<project>/` или `work/troubleshooting/`; они не публичны и к контрактам агента не относятся. Зеркала ADR продуктов: `work/projects/door-to-singularity/cdp-mcp/` (серия `CDP-ADR-02xx`), `work/projects/door-to-singularity/cascade-ide/` (серия `CDP-ADR-00xx`). Перенесено 2026-10-28: 8 файлов → `cdp-mcp/`, 2 → `cascade-ide/`, 1 → `opencode-habitat/`, 3 инцидента Cursor → `work/troubleshooting/`, 2 (guiders-fsharp WORK + дочерняя нота) → `guiders-core/`.

**Hub:** [`agent-memory-and-operating-principles.md`](../../agent-memory-and-operating-principles.md) — маршрутизатор, не дубль тел.

**Product (CascadeIDE):** [cascade-ide/playbook-agent-execution-environment.md](../work/projects/door-to-singularity/cascade-ide/playbook-agent-execution-environment.md) — AEE, verify ladder; SSOT — ADR 0148 в репо IDE. `knowledge/kb-protocols-and-entities-one-pager.md`, `knowledge/kb-one-pager-structure-and-protocols.md`.

