---
type: Evidence Card
title: 'Evidence: Aristotle — Sophistical Refutations (первый каталог уловок)'
description: 'Тринадцать уловок, разделённых на языковые (6) и внеязыковые (7). Первая систематика различения подлинного и мнимого опровержения.'
tags: [argumentation, philosophy, paper-evidence, aristotle, worlds]
language: ru-RU
epistemic: synthesis_from_primary_literature
world: philosophy
subworld: argumentation
---

# Evidence: Aristotle — Sophistical Refutations (De Sophisticis Elenchis)

## Provenance

- source_refs:
  - primary: Aristotle. *De Sophisticis Elenchis* (Περὶ Σοφιστικῶν Ἐλέγχων). ~350 BCE. Входит в Органон.
  - text_consulted: [Logos Library — Chapter 1](http://www.logoslibrary.org/aristotle/sophistical/01.html) (public domain перевод); [Loeb Classical Library](https://www.loebclassics.com/abstract/aristotle-sophistical_refutations/1955/pb_LCL400.63.xml) (E.S. Forster перевод, abstract open)
  - secondary: [Krabbe (2012)](https://philpapers.org/rec/KRAAOS); [Nemos Library](https://nemoslibrary.com/2011/07/02/on-sophistical-refutations-by-aristotle)
- created_at: 2026-11-02
- extraction_note: Текст public domain. Карточка по переводу + вторичным источникам.

## Metadata

- world: philosophy.argumentation
- layer: knowledge
- status: active

## Epistemic linkage

- evidence_type: primary text (перевод) + вторичные обзоры
- confidence: **high** для деления на 6+7 уловок; **medium** для точных названий категорий (разные переводы дают разные термины)
- falsification_trigger: если operational детектор уловок не мапится на аристотелевские категории без ad hoc полей — пересмотреть enum.

## Fundamentals

### K1 — Мнимое опровержение

Софистическое опровержение — то, что **выглядит** как опровержение, но **является** ошибкой. Это первая систематика различения подлинного и мнимого.

### K2 — Два класса уловок

Все уловки делятся на **зависящие от языка** (6) и **независящие от языка** (7). Языковые — эксплуатируют многозначность слов и конструкций; внеязыковые — эксплуатируют логические ошибки вывода.

### K3 — Шесть языковых уловок

1. **Equivocation** (омонимия) — одно слово в двух значениях
2. **Amphiboly** (двусмысленность) — грамматическая неоднозначность
3. **Composition** — объединение раздельного в целое
4. **Division** — разделение целого на части
5. **Accent** — ударение/интонация меняет смысл
6. **Figure of speech** — одинаковое выражение, разные функции

### K4 — Семь внеязыковых уловок

1. **Accident** — приписывание случайного свойства необходимому
2. **Secundum quid** — от условного к безусловному
3. **Ignoratio elenchi** — опровержение не того тезиса (missing the point)
4. **Begging the question** (petitio principii) — тезис в посылках
5. **Consequent** — утверждение консеквента
6. **False cause** (post hoc) — ложная причинность
7. **Complex question** — вопрос с presupposition

### K5 — Три мотива спора

Если мотив — видимость победы: **contentious** (сварливый). Если видимость мудрости: **sophistical** (софистический). Если истина: **dialectical** (диалектический). Софистика = видимость мудрости без реальности.

### K6 — Проверка опровержения

Подлинное опровержение: тезис противника **действительно** следует из его посылок, и опровержение показывает противоречие. Мнимое: противоречие только **кажется**. Проверка: верны ли посылки? следует ли тезис? есть ли действительное противоречие?

## Operational bridges

- **XL**: ложные пары где A — уловка из каталога, B — правильный аргумент
- **agent-operations/principled-clarity**: ignoratio elenchi = уклонение от позиции
- **psychology.models/манипуляция**: языковые уловки = распознавание в диалоге
