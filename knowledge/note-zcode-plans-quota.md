---
type: Note
title: 'Note: ZCode планы и квоты — общий пул 5.3/Flash'
description: 'Individual Coding Plan: weekly-лимит общий на 5.3 и Flash (не раздельный); idle 5 задач/день сбрасывают 5-часовое окно. Практика экономии — переход на Flash.'
tags: [zcode, quota, plans, environment, agent-operations]
language: ru-RU
---
# ZCode планы и квоты

Наблюдения оператора (2026-11-03):

- **Individual Coding Plan: weekly-лимит — общий пул** на GLM-5.3 и GLM-5.3-Flash. Переход на Flash экономию **не даёт** — он лишь дешевле по reasoning-поведению... на Start Plan тарификация была раздельной (5.3 отдельно, Flash отдельно), здесь — общий пул.
- Практика при приближении к лимиту: перейти на Flash (меньше расход на ход за счёт лёгкого reasoning, не за счёт тарифа), тяжёлые задачи отложить до сброса Weekly или в idle-очередь (бесплатно, 5/день).
- Idle-часы сбрасывают 5-часовое окно квоты (см. [note-zcode-idle-tasks.md](note-zcode-idle-tasks.md)).
- Highspeed card — отдельный счёт: те же две модели, свой пул.
- Список моделей: `account:zai-individual-coding-plan/*` (2), `account:zai-offpeak-idle-plan/*` (2), `account:zai-highspeed-card/*` (2). Реестр серверный — дубль записи с другим контекстом завести нельзя («Model 已存在»).
- Контекст 1M — свойство модели на сервере; урезанию при регистрации не поддаётся. Для экспериментов с окном — см. [note-zcode-lost-in-middle-experiment.md](note-zcode-lost-in-middle-experiment.md).
