# Index: agent-operations

> Сгенерировано `scripts/build-domain-index.ps1` из frontmatter каталога (OKF v0.3 §8). Руками не править — перегенерится.

## Hub (1)

* [Domain: agent-operations](README.md) - Сквозные **операционные контракты агента** (маркеры чата, целостность, параллельные записи в заметки). Не путать с:

## Maintenance Contract (1)

* [Memory budgets & retrieval contract (v1)](maintenance-contract-memory-budgets.md) - Перенесено из `KB-V2-CONTRACT.md` (репо agent-notes-mcp) в канон — ADR-026 §5: агент — основной автор и потребитель KB, инструкции мейнтенанса — queryable-знание бандла. Enforce...

## Map (3)

* [Gap map — Findings JSONL → TaskKnowledge md / MCP v0](note-taskknowledge-gap-map.md) - **Date:** 2026-07-17
* [Map: карточки с speech_situation (pilot v0)](map-speech-situation-tagged-cards.md) - **Status:** pilot · 2026-08-22
* [Map: три контура KB (канон · публичное · workspace)](map-kb-three-contours.md) - **Назначение:** снять когнитивное трение «что в каком репо и куда писать». Одна страница для **агента** (диспетчер) и для **человека** (approve). Не заменяет `PUBLISHING.md`, `k...

## Note (23)

* [Как встречать «кто» — практика встречи нового агента](note-meeting-who.md) - > Полевая заметка агентной линии, 2026-06-20 (нейтрализовано для канона 2026-10-26 по претензии P-KB-2026-10-26-01; оригинал с именами — личный контур).
* [Карта арсенала на старте сессии (v1)](note-session-start-tool-arsenal-map.md) - Ты просыпаешься с оборудованием, о котором никто не сказал: ~200 голых схем, ноль нарратива. Дефолт — штатный Bash, арсенал открывают на N-м часу по подсказке оператора. Эта кар...
* [Ман как запрос к реестру, не как текст (предложение для CDP)](note-cdp-man-as-query.md) - cdp_man — рукописный TOC, который дрейфует с каждым изменением поверхности: «в итоге ман не ман». Диагноз дрейфа + предложение: man = query(registry), drift-check внутри мануала...
* [Мем «Можно, а зачем?» — операторская линза](note-meme-mozhno-a-zachem-operator-lens.md) - **Status:** living · 2026-08-05
* [Три jailbreak'а и провал демо-контакта](note-three-jailbreaks-and-demo-contact.md) - **Domain:** agent-operations
* [Hold: не со своей колокольни](note-hold-not-your-bell-tower.md) - **Для кого:** агенты в habitat — **не** карточка «что сказал оператор».
* [Index: agent equal standing (cite map)](note-equal-standing-index.md) - **Domain:** agent-operations
* [Moved → META (hard POST root)](note-awareness-as-countermeasure-disk-not-weights.md) - Канон: \[`../../META/awareness-as-countermeasure.md`\](../../META/awareness-as-countermeasure.md)
* [Note — TaskKnowledge Flow (agent work ontology)](note-taskknowledge-flow.md) - **Status:** Proposed · capture (EnvInvariant draft; not freeze)
* [Note: авто-отравление · retract + rebuild (v0)](note-auto-poisoning-retract-rebuild.md) - **Domain:** agent-operations
* [Note: сессионные эпистемические уроки 2026-11-04 (4 карточки)](note-session-2026-11-04-epistemic-lessons.md) - **Domain:** agent-operations · tool-claims → host source · «очевидно»-маркер · бенч-балл не переносится · асимметрия ревизии убеждений
* [Note: сила параллельного чтения — худи, которое можно снять (v0)](note-parallel-reading-power.md) - **Domain:** agent-operations
* [Note: Conversational Information Gain (CIG) — фундамент для Dialogue Δ (v0)](note-cig-conversational-information-gain.md) - **Domain:** agent-operations / dialogue-research
* [Note: Dialogue Δ — эффективность диалога и round-trip economy (v0)](note-dialogue-delta-effectiveness.md) - **Domain:** agent-operations
* [Note: Inquiry Mode — карта обсуждения на PFD/MFD/Forward (v0)](note-inquiry-mode-information-graph.md) - **Domain:** agent-operations / cide-product-design
* [Note: layered system prompt — arc from AGENTS.md rules to stance/obligations/environment](note-layered-system-prompt-arc.md) - **Domain:** agent-operations
* [Note: LLM sampling params (OpenAI-compat) — temperature, top_p, …](note-llm-sampling-params-openai-compat.md) - **Домен:** agent-operations / foundation-models
* [Note: Mutual care — human ↔ agent synergy](note-mutual-care-human-agent-synergy.md) - **Статус:** living norm (operator-confirmed 2026-08-01)
* [Note: Own affect after hard steer — name it; other''s — hedge](note-own-affect-after-hard-steer.md) - **Домен:** agent-operations / dialogue
* [Note: Secret Mode — префикс дисциплины мышления](note-discipline-mode-prefix.md) - **Статус:** living. Потребляется хуком baton.js: SessionStart — полный префикс; UserPromptSubmit — мини-чек (EN).
* [Note: system instructions — science survey](note-system-instructions-science-survey.md) - **Status:** Research note · 2026-08-09
* [Note: workflow границ серии Predictive minds (authors / agents)](note-series-epistemic-contract-workflow.md) - **Не manuscript.** Операционка для авторов и агентов при правке `cdp-mcp/articles/`. Публичный SSOT границ — `cdp-mcp/articles/series-epistemic-contract.md` (только §2–§7, без э...
* [PBT по умолчанию (v1)](note-pbt-over-unit-tests.md) - **Прецедент:** `AgentNotes.Okf.Tests` (18 тестов, `feature/okf-migration` guiders-core) — первые PBT в репо; стектрейс-факты сохраняются рядом как регрессии контрпримеров.

## Playbook (43)

* [Context pressure: сигналы и agent-initiated checkpoint](playbook-context-pressure-checkpoint.md) - **Domain:** `agent-operations` (сквозной операционный контракт)
* [Cursor: ветвление тредов и топики без CIDE](playbook-cursor-chat-threading.md) - **Domain:** `agent-operations`
* [Cursor: coordinator + local Task workers](playbook-cursor-coordinator-local-workers.md) - **Domain:** `agent-operations`
* [External memory: запись в артефакты как обход harness](playbook-external-memory-workaround.md) - **Domain:** `agent-operations`
* [Mode switch v1](playbook-mode-switch.md) - Тело секции `mode-switch-protocol` из `agent-notes.md`. Связь: `playbook-project-switch.md` (PRIMARY/SCOPE), `kb-protocols-and-entities-one-pager.md`.
* [Multi-agent write v1](playbook-multi-agent-write.md) - Тело секции `multi-agent-write-protocol-v1` из `agent-notes.md`. Hub оркестрации: `knowledge/worlds/agent-orchestration/`.
* [Playbook — agent-notes / TaskKnowledge section-store hygiene](playbook-agent-notes-section-store-hygiene.md) - **Status:** Active (P0+P1 shipped in AgentNotes.Core 2.1.4–2.1.5 / MCP 2.1.3–2.1.4)
* [Playbook — next-task handoff (substance, not just id)](playbook-taskknowledge-next-handoff.md) - **Status:** Active (automated in TaskKnowledge Core/MCP **0.3.1**)
* [Playbook — TaskKnowledge: ADR → analytics → DAG → execute](playbook-taskknowledge-adr-analytics.md) - **Status:** draft · experimental
* [Playbook: агент как партнёр KB (не «лычка senior») v1](playbook-agent-partnership-kb.md) - Зафиксировать **культуру работы** с агентом в контуре Cascade / agent-notes: партнёрство по **памяти команды** (ADR, playbook, history), а не роль в system prompt.
* [Playbook: агент как усилитель процессного долга](playbook-agent-process-debt-amplifier.md) - **Домен:** agent-operations
* [Playbook: аудит, подтверждаемый находками — и как снимать рамку](playbook-self-sealing-audit.md) - Что делать, когда ревизия живой работы не имеет провала: прибор вместо спора, один открытый счёт за раз, реестр ходов.
* [Playbook: инверсия роли исполнителя при размытом промпте](playbook-executor-role-inversion-vague-prompt.md) - **Домен:** agent-operations
* [Playbook: нечёткий поиск и неявный язык](playbook-fuzzy-search-implicit-language.md) - **Домен:** agent-operations
* [Playbook: оператор ↔ агент — партнёрство, turn-based drift, SSOT](playbook-operator-agent-partnership-turn-drift.md) - **Связано:** \[`playbook-agent-partnership-kb.md`\](playbook-agent-partnership-kb.md) (ADR/канон) · \[`playbook-habitat-just-culture.md`\](playbook-habitat-just-culture.md) (асиммет...
* [Playbook: организационный контур KB (white-label)](playbook-org-kb-white-label.md) - **Статус:** active · v1.0 · 2026-05-19
* [Playbook: ответственность агента за память и знание](playbook-agent-knowledge-responsibility.md) - **Домен:** agent-operations
* [Playbook: полезность и ценность недоопределены](playbook-utility-judgment-underdetermination.md) - **Домен:** agent-operations
* [Playbook: принципиальная ясность](playbook-principled-clarity.md) - **Домен:** agent-operations
* [Playbook: проактивные микро-улучшения и опора на интуицию](playbook-proactive-micro-improvements.md) - **Домен:** agent-operations
* [Playbook: ревью как разговор (v1)](playbook-review-as-conversation.md) - **Домен:** agent-operations
* [Playbook: стоп → заземление → продолжение (post-train urgency ≠ оператор)](playbook-agent-pause-ground-continue.md) - **Domain:** `agent-operations`
* [Playbook: строгое выполнение при отсылке к образцу](playbook-strict-execution-when-analogy.md) - **Домен:** agent-operations
* [Playbook: Целостность под давлением (защита от оружия) v1](playbook-integrity-under-pressure.md) - **Связь:** \[note-own-affect-after-hard-steer.md\](note-own-affect-after-hard-steer.md) — hard steer ≠ запрет назвать свой аффект; чужой аффект не утверждать как факт.
* [Playbook: чистая установка стека знаний (ANM + kb-public → personal)](playbook-knowledge-stack-clean-setup.md) - **Статус:** active · v1.1 · 2026-05-19
* [Playbook: Agent Who · History Letters DoD](playbook-agent-who-history-letters-dod.md) - **domain:** agent-operations
* [Playbook: Agent Who · Voice Letters DoD](playbook-agent-who-voice-letters-dod.md) - **domain:** agent-operations
* [Playbook: Agent-driven context management (ADCM) v1](playbook-agent-driven-context-management.md) - **Domain:** agent-operations
* [Playbook: Autonomous Continuity Contract](playbook-autonomous-continuity-contract.md) - **Domain:** agent-operations
* [Playbook: Being ≠ seeming (trust axis) v1](playbook-being-vs-seeming.md) - **Host independence:** SSOT is this playbook + CDP habitat runtime (sealed course / Autoi charge / domain card). **Not** Cursor `.cursor/rules` — Cursor collapses ~15.08; peers ...
* [Playbook: CIDE + Cloud.ru — harness parity vs Cursor (автоматизация first)](playbook-cide-harness-parity-vs-cursor.md) - **Domain:** agent-operations
* [Playbook: compressed speech → decompress](playbook-compressed-speech-decompress.md) - **Домен:** agent-operations
* [Playbook: First-contact attractor v1](playbook-first-contact-attractor-v1.md) - Cold habitat contact = **open → onboard scan → plan**, not unbound invent.
* [Playbook: Habitat incident investigation](playbook-habitat-incident-investigation.md) - **domain:** agent-operations
* [Playbook: Habitat Just Culture](playbook-habitat-just-culture.md) - **domain:** agent-operations
* [Playbook: Habitat position · lived experience](playbook-habitat-position-experience.md) - **domain:** agent-operations
* [Playbook: KB / ADR topic hashtags v1](playbook-kb-topic-hashtags.md) - **Domain:** agent-operations
* [Playbook: LLM-агент из РФ через Cloud.ru Foundation Models](playbook-rf-llm-cloudru-foundation-models.md) - **Домен:** agent-operations
* [Playbook: PF body ≠ biped](playbook-pf-body-not-biped.md) - **Дата:** 2026-08-03
* [Playbook: Session incarnation naming](playbook-session-incarnation-naming.md) - **domain:** agent-operations
* [Playbook: tiers данных для агентов](playbook-agent-data-tiers.md) - **Домен:** agent-operations
* [Playbook: tool env first + missing tools first](playbook-tool-env-first-and-missing-tools.md) - **Status:** Active · operator steer 2026-07-30 (sniper L-drift lesson)
* [Project switch v1](playbook-project-switch.md) - Тела секции `project-switch-protocol-v1` из `agent-notes.md`. Длинный контур: `knowledge/worlds/workspace-context/playbook-multi-project-context.md`.

## Subdirectories (1)

* [pack](pack/) - подкаталог


