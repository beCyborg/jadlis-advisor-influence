[Русский](README.md) · English

# advisor-influence — Claude Code plugin

Command: `/advisor-influence`.

## Было → стало

To be written (README contract 2026-09, phase 3).

## Как это работает

Пятнадцать советников по маркетингу и влиянию (14 книг плюс тактический синтез 2026) разбирают запрос через призму своей книги, валидатор сводит вердикт с SWOT и картой консенсуса.

## Установка и первый запуск

```bash
claude plugin marketplace add https://github.com/beCyborg/jadlis-start.git
claude plugin install advisor-influence@jadlis --config MEMORY_DIR=~/advisors-memory
```

## Границы, стоимость, обновление

Book digests are derivative works, no licence: see [NOTICE.md](NOTICE.md). This repository is generated from a private source; open issues here, edits land in the source.

```bash
claude plugin marketplace update jadlis
claude plugin update advisor-influence@jadlis
```
