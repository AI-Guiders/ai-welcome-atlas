---
type: ADR
title: 'ADR 027: Убрать .revisions и autoship — MCP сам зовёт git'
status: stable
tags: [adr, agent-notes-mcp, revisions, git, autoship, vcs]
language: ru-RU
---
# ADR 027: Убрать .revisions и autoship — MCP сам зовёт git


**Статус:** Accepted (operator steer 2026-11-02)  
**Дата:** 2026-11-02  
**Supersedes:** —  
**Extended by:** —

---

## Контекст

Два параллельных механизма поверх git:

1. **`.revisions/`** — agent-notes-mcp сохраняет копию файла перед записью (с апреля 2026, 463 файла, 6.2 МБ)
2. **AutoShip** (CDP-ADR-0224) — FileSystemWatcher на корнях KB → debounce 90с → fetch → secret-scan → git add -A → commit → push

Git работает с **февраля 2026** — раньше обоих. Оба дублируют его функцию.

## Решение

**MCP сам выполняет git-операции на каждой записи. Autoship и .revisions упраздняются.**

### Изменяющие операции MCP

`write_knowledge_file`, `upsert_knowledge_section`, `append_knowledge_file`, `delete_knowledge_file`:

```
1. git status --porcelain <file>  → если dirty от чужих правок: отказ
2. Записать файл
3. git add <file>
4. git commit -m "MCP: <операция> <файл>"
```

Не `git add -A` (хватит только изменённого файла). Не debounce (коммит сразу). Не fetch-gate (MCP знает, что только он писал).

### Push

- Не на каждой записи (дорого для remote)
- Периодический тик (cron / фоновый job раз в N минут): `git push`
- Или по явной команде оператора
- MCP может отдавать warning: «unpushed commits: N»

### Git обязателен

MCP при старте: `git rev-parse --git-dir`. Если не найден — явное сообщение: «поставьте git». Не фолбэк, не свои ревизии.

### Rollback

`git log --follow <file>` + `git checkout <commit> -- <file>`. Не сканирование `.revisions/`.

## Что упраздняется

| Механизм | Строк кода | Что делал | Кто теперь делает |
|----------|-----------|-----------|-------------------|
| `.revisions/` | ~200 | Сохранял копии перед записью | git commit |
| KbAutoShipService | ~500 | FileSystemWatcher → debounce → fetch → add -A → commit → push | MCP: add + commit; push по тику |
| KbAutoShipEngine | ~300 | Оркестрация ship-цикла | Не нужен |
| KbAutoShipGitRunner | ~150 | Запуск git с timeout/kill | MCP вызывает git напрямую |
| outbox-очередь | ~100 | Накопление изменений для синка | Не нужна — коммит сразу |
| fetch-gate | ~50 | Проверка расхождения с remote | git push отклонит non-fast-forward |

**Итого: ~1300 строк инфраструктуры → ~30 строк (add + commit на запись, push на тик).**

## Почему это лучше

| | Было (autoship + revisions) | Стало (MCP + git inline) |
|---|---|---|
| Каждая запись | файл + revision + watcher + debounce + commit | файл + commit |
| Commit message | "KB auto: <file>" (генерик) | "MCP: upsert section <id> in <file>" (осмысленный) |
| Задержка до коммита | 90+ секунд (debounce) | Мгновенно |
| Конфликты | fetch-gate + rebase | git push отказывает non-fast-forward |
| Аварии | Timeout / lock / частичный push | git атомарен |
| Code | ~1300 строк | ~30 строк |

## Миграция

1. Убрать save_revision из MCP-тулов (флаг → false → потом удалить код)
2. Убрать KbAutoShip* из CdpService
3. Добавить git add + commit в MCP write-операции
4. Добавить periodic push (простой cron / timer)
5. Существующие `.revisions/` — deprecated, удалить одним мержем
6. CDR-ADR-0224 (autoship) — пометить superseded этим ADR

## Без remote

Git-local не требует ни сервера, ни сети. Только бинарник. Для среды без интернета — git с флешки.
## Конфигурация

```toml
[git]
path = ""  # пусто = git из PATH; не пусто = полный путь к бинарнику
```

### Проверка при старте

```
git_path = config.git.path || "git"
Process.Start(git_path, "rev-parse --git-dir")
  → ok: работаем
  → fail: сообщение оператору: "git не найден по пути <path>. Укажите путь в конфиге или установите git."
```

<!-- section:adr-027-implementation -->
**Реализовано 2026-11-02, AgentNotesMcp 2.2.0** (guiders-core `AgentNotes.Core` 3.0.0):

- `GitHelper` — ArgumentList-обёртка git CLI (`rev-parse/show-toplevel`, `status --porcelain`, path-scoped `add+commit`, `push`, startup-диагностика), `GIT_TERMINAL_PROMPT=0`.
- Knowledge-операции (`write/append/upsert/delete file/delete section/normalize`): dirty-check до записи → отказ поверх незакоммиченных внешних правок; после записи — мгновенный path-scoped коммит `MCP <операция>: <путь>`.
- `save_revision` удалён из схем тулов и кода; `.revisions` больше не пишутся. `list_agent_notes_revisions`/`rollback_agent_notes` — LEGACY: читают существующие `.revisions`, новых не создают.
- Hot notes: atomic write + git commit (только KB-репо или top-level notes); снапшоты убраны.
- Конфиг `[git]path = ""` (пусто = PATH); при старте `--version` + `rev-parse` + `user.name` — иначе exit 3 с сообщением оператору.
- Тесты: 75/75, включая новые `GitInlineKnowledgeTests` (real git: commit-on-write, dirty-refuse, commit-on-delete).

Осталось: убрать KbAutoShip* из cdp-mcp (миграция шаг 2), periodic push-тик, удалить legacy `.revisions` одним мержем.
<!-- /section:adr-027-implementation -->


