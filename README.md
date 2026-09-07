Русский · [English](README.en.md)

# Медийность и доверие без «AI-инфлюенсера»

Плагин `advisor-influence` для Claude Code. Команда — `/advisor-influence`.

## Было → стало

Раздел заполняется по контракту README 2026-09 (фаза 3 плана «GitHub beCyborg как витрина Jadlis»).

## Как это работает

Пятнадцать советников по маркетингу и влиянию (14 книг плюс тактический синтез 2026) разбирают запрос через призму своей книги, валидатор сводит вердикт с SWOT и картой консенсуса.

## Установка и первый запуск

```bash
claude plugin marketplace add https://github.com/beCyborg/jadlis-start.git
claude plugin install advisor-influence@jadlis --config MEMORY_DIR=~/advisors-memory
```

## Границы, стоимость, обновление

Конспекты книг — производные работы, лицензии нет: см. [NOTICE.md](NOTICE.md). Правки принимаются только в источнике (`jadlis-advisors-source`), этот репо генерируется.

```bash
claude plugin marketplace update jadlis
claude plugin update advisor-influence@jadlis
```
