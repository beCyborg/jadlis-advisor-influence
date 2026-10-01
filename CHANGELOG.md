# Changelog — advisor-influence

Формат: [Keep a Changelog](https://keepachangelog.com/ru/1.1.0/), версии — [SemVer](https://semver.org/lang/ru/).
До 1.0.0 совет жил в плагине `advisors` 1.0.0–1.0.3 (репо `advisors`, теперь приватный источник `jadlis-advisors-source`).

## [Unreleased]

## [1.0.6] — 2026-10-02 — Проверка args до первого агента / Args guard before the first agent

### Для человека

- Если аргументы совета не разобрались (кавычки „“ или голая " внутри значения), пришли пустыми (повтор прогона без аргументов) или в них не хватает ключа, совет теперь сразу падает с понятной ошибкой и не запускает ни одного агента. Раньше он молча прогонял весь совет на тестовых данных.
- Рабочая папка прогона должна быть абсолютным путём; если папка памяти задана относительным путём, скилл делает её абсолютной и говорит об этом.

### For agents

- Changed: every engine in `workflows/` — one guard block right after args normalisation: a string that fails `JSON.parse` throws (script name, parse error, 300-char preview, hint about „“/unescaped `\"` and that resume does not inherit args); dry-run defaults only with `args.dryRun === true`; otherwise required keys must be non-empty and `workDir` absolute, else throw before any `agent()`.
- Changed: `workflows/council-influence.js` — required without `dryRun`: query, advisors, workDir, pluginRoot.
- Changed: `workflows/council-question-harvest.js` — required without `dryRun`: query, roster, councilType, workDir.
- Changed: `skills/advisor-influence/SKILL.md` Phase A.0 step 2 — a relative `MEMORY_DIR` (after `~` expansion) is made absolute (`$(pwd)/…`) and the user is told.
- Migration: callers that relied on silent defaults must pass the full args; a deliberate test run passes `dryRun: true`. A resumed run must pass the same args again.

## [1.0.5] — 2026-09-23 — Контракт прогона / Run contract

### Для человека

- В скилл добавлен короткий «контракт прогона»: прогон закончен, когда вердикт лежит в папке `Медийность`, показан целиком, а в журнал советов дописана строка.
- Совет останавливается только там, где нужен ваш ответ (интервью, пустой запрос, не задана папка памяти), и не заканчивает ход вопросом «Продолжить?».

### For agents

- Added: `skills/advisor-influence/SKILL.md` — `## Run contract` before `## Phase A.0`: Done when (verdict in `{OUTPUT_DIR}`, `{WORK_DIR}` removed, journal line `adv-influence`, profile sessions[]; low-quorum → no file), keep-going rules, own confirm steps (A.0, A.1, A.5 interview, Phase C follow-up), ledger as the subagent check + one SUPPORTED spot-check, `{WORK_DIR}/progress.md`, final report shape.
- Migration: none.
- Refs: Anthropic guide «Getting the most out of Opus 5.5» (adopted 2026-09-23) — no requests to show reasoning; every skill states what "done" means and when to stop.

## [1.0.4] — 2026-09-22 — Effort по ролям / Effort by role

### Для человека

- Итоговый вердикт теперь пишет валидатор на повышенном уровне рассуждений `xhigh`; 15 советников, куратор и скептики остаются на `high`.
- Кластеризатор уточняющих вопросов работает на `medium` — это механическая дедупликация.
- Почему: исследование Opus 5.5 от 22.09.2026 — прирост `xhigh` измерен на итоговых документах (GDPval +128 Elo) и стоит немного, когда агент один в конце цепочки; на веерах он не окупается, поэтому они остаются на `high`.

### For agents

- Changed: `workflows/council-influence.js` — `validator` step: `effort: 'xhigh'` in the `agent()` options.
- Changed: `workflows/council-question-harvest.js` — `question-clusterer` step: `effort: 'medium'` (mechanical dedup).
- Unchanged: advisors, curator, skeptics (`high`), question askers (`low`).
- Migration: none. The per-call `effort` overrides the worker frontmatter `effort: high` and is merged after a caller-supplied `workerOpts`, so it applies in the fallback path too.
- Refs: Opus 5.5 study 2026-09-22 — `xhigh` gains are measured on final documents (GDPval-AA 1692 → 1820, +128 Elo) and cost little on a single agent at the end of the chain; fan-outs stay `high`.

## [1.0.3] — 2026-09-22 — Переход на Opus 5.5 / Switch to Opus 5.5

### Для человека

- Совет целиком — советники, скептики и валидатор — теперь работает на новой модели Opus 5.5 с окном контекста в 1 млн токенов.
- Требует Claude Code ≥ 2.1.280: более старые версии могут не знать эту модель — сначала обновите Claude Code, потом совет.
- Effort, состав линз, формат вердикта и папка памяти не менялись.

### For agents

- Changed: `agents/advisor-opus.md` — `model: opus` → `model: claude-opus-5-5` (`effort: high` unchanged).
- Changed: `skills/advisor-influence/SKILL.md` — frontmatter `model: opus` → `model: claude-opus-5-5` (`effort: high` unchanged); the worker override hint now reads `workerOpts: { model: 'claude-opus-5-5' }`.
- Changed: `workflows/council-influence.js` — header comment: worker pinned to Opus 5.5; documented fallback `workerOpts: { model: 'claude-opus-5-5' }` (was `'opus'`). Code path unchanged.
- Migration: requires Claude Code ≥ 2.1.280 — older versions may reject the unknown model ID `claude-opus-5-5` when loading the skill or spawning `advisor-influence:advisor-opus`; update Claude Code (`claude update`). The 1M context window is native to the model — no `[1m]` suffix. Callers that pass their own `workerOpts` are unaffected.

## [1.0.2] — 2026-09-08

### Для человека

- Обновление из источника jadlis-advisors-source.

### For agents

- Regenerated by `tools/split-councils.py`; see the source repo commit in `SHARED_FROM.txt`.

## [1.0.0] — 2026-09-07

### Для человека

- Первый релиз под именем `advisor-influence`: выделен из `advisors` 1.0.3 — репо на совет, команда `/advisor-influence`, ключ настройки `MEMORY_DIR`.

### For agents

- Generated by `tools/split-councils.py` of `jadlis-advisors-source`; worker agent `advisor-influence:advisor-opus`; `SHARED_FROM.txt` carries the source tag + content hash checked by CI.
