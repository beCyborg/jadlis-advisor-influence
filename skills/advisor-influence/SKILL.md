---
name: advisor-influence
user-invocable: true
argument-hint: "<ваш вопрос по маркетингу, влиянию или росту аудитории>"
allowed-tools:
  - Read
  - Write
  - Bash
  - AskUserQuestion
  - Workflow
model: opus
effort: high
description: |
  Совет 15 AI-советников: 14 маркетинговых/influence книг + тактический синтез 2026.
  Каждый советник анализирует запрос через призму одной книги, затем validator
  синтезирует единый вердикт с SWOT, картой консенсуса и actionable рекомендациями.
  14 книг: Influence (Cialdini), $100M Leads (Hormozi), This Is Marketing (Godin),
  Made to Stick (Heath), Contagious (Berger), StoryBrand (Miller), Hook Point (Kane),
  Day Trading Attention (GaryVee), Show Your Work! (Kleon), Traction (Weinberg/Mares),
  Obviously Awesome (Dunford), Never Eat Alone (Ferrazzi), KNOWN (Schaefer),
  Superfans (Flynn).
  Плюс 15-й советник «Тактика 2026» — не книга, а синтез исследований платформ
  (алгоритмы, воронка доверия, KPI; data_as_of 2026-07, стареет быстро).
  Invoke via /advisor-influence со своим вопросом.
  English triggers: marketing advice, influence strategy, growth strategy, audience building,
  content strategy, lead generation, persuasion, branding, positioning, customer acquisition,
  viral marketing, social media strategy, book council, advisor council, marketing council.
  Russian triggers: совет по маркетингу, стратегия влияния, стратегия роста, рост аудитории,
  контент-стратегия, лидогенерация, убеждение, брендинг, позиционирование,
  привлечение клиентов, вирусный маркетинг, стратегия соцсетей, совет книг,
  совет советников, маркетинговый совет, совет экспертов.
---

# Advisor Council — 15 советников по маркетингу и influence (гибрид Skill + Workflow)

Тяжёлая часть (15 советников → cross-verify claims → синтез вердикта) исполняется
детерминированным workflow **`council-influence`**. Скилл делает интерактивный intake
(уточнение + preflight путей) и запись результата.

## Константы

```
PLUGIN_ROOT = ${CLAUDE_PLUGIN_ROOT}
MEMORY_DIR  = ${user_config.MEMORY_DIR}
OUTPUT_DIR  = {MEMORY_DIR}/Медийность
PROFILE     = {MEMORY_DIR}/Профили/adv-influence.md
RUN_LOG     = {MEMORY_DIR}/Журнал советов.md
WORK_DIR    = {MEMORY_DIR}/_runs/influence-{QUERY_SLUG}
```

> [!tip] Перед запуском и перед применением правок
> Прочитай `${CLAUDE_PLUGIN_ROOT}/shared/council-verdict-playbook.md` (Read по требованию):
> сессионное окно валит фан-аут целиком, консенсус фан-аута опровергается чаще, чем кажется,
> и правки применяются только из вердикта.

Внутри протоколов, линз и общих контрактов пути записаны плейсхолдерами `{PLUGIN_ROOT}` и
`{MEMORY_DIR}`: подстановка `${CLAUDE_PLUGIN_ROOT}` и `${user_config.*}` в читаемые файлы не
доходит. Подставляй значения сам; литеральный `{PLUGIN_ROOT}` в Read не отправляй.

## Phase A.0 — гейт памяти (первым, каждый запуск)

1. `MEMORY_DIR` пуст **или** в нём буквально видно `${user_config` → **остановиться**:
   > Не задана папка памяти советов. Открой `/plugin` → advisor-influence → настройки и укажи
   > `MEMORY_DIR` (например `~/advisors-memory`), либо переустанови плагин с
   > `--config MEMORY_DIR=<путь>`. Вердикты в текущую рабочую папку совет не пишет.
2. Путь начинается с `~/` → заменить `~` на `$HOME` **до любой записи**.
3. Развернуть скелет — идемпотентно, существующие файлы не трогает; если папка создана
   впервые, сказать об этом и перечислить, что в ней появилось:
   ```bash
   bash "${CLAUDE_PLUGIN_ROOT}/scripts/init-memory.sh" "{MEMORY_DIR}" "${CLAUDE_PLUGIN_ROOT}"
   ```
4. `mkdir -p "{OUTPUT_DIR}"` — подпапка вердиктов этого совета.
5. Ни один шаг скилла не пишет за пределы `{MEMORY_DIR}`.

## Архитектура

```
Phase A (INTAKE, главная сессия) → Phase B (Workflow council-influence) → Phase C (WRITE)
```

## Phase A — INTAKE

1. **Запрос.** Если `$ARGUMENTS` пуст → AskUserQuestion: «Какой вопрос задать совету
   15 экспертов по маркетингу и influence?». Иначе `RAW_QUERY = $ARGUMENTS`.
2. **Память.** Прочитай `{PROFILE}` (если есть) → `USER_CONTEXT` (продукт, целевая
   аудитория, стадия, текущие каналы). Файл содержит постоянную секцию `## Profile`
   (цель, платформы, ограничения, позиционирование) — она входит в USER_CONTEXT целиком
   и **исключена из ротации памяти** (Phase C её не переписывает, только читает).
3. **Preflight ростера.** Для каждого советника проверь `test -d "{skillPath}/references"`;
   путь не найден → **исключи** советника с warning (он уйдёт в знаменатель кворума).
4. **Сборка SCOPE.** `QUERY_SLUG` (lowercase, спецсимволы→дефис, ≤50) — ТОЛЬКО для WORK_DIR.
   `FILE_NAME` — имя файла вердикта: короткое **русское** название темы (3–7 слов), без
   символов `/ \ : # ^ [ ] |`; перед записью проверь коллизию имён `ls "{OUTPUT_DIR}"`,
   имя занято → суффикс « (2)».
   `OUTPUT_DIR` и `WORK_DIR` — из блока «Константы»; создай их:
   `mkdir -p "{OUTPUT_DIR}" "{WORK_DIR}"`. Артефакты прогона живут в `{MEMORY_DIR}/_runs/`
   и удаляются в Phase C.
   Собери массив `advisors` (минус исключённые на preflight).
5. **Интервью (обязательно).** По протоколу `${CLAUDE_PLUGIN_ROOT}/shared/council-interview-protocol.md`
   (Read по требованию — файл НЕ в контексте). Кратко: KNOWN_MAP из запроса+памяти
   (секция `## Profile` = «Известно (из памяти)» — НЕ переспрашивать то, что там есть) →
   `Workflow(scriptPath:"${CLAUDE_PLUGIN_ROOT}/workflows/council-question-harvest.js",
   args:{query: RAW_QUERY, userContext: USER_CONTEXT,
   roster: advisors→{slug, name: "{book} ({author})", skillPath}, councilType: "influence",
   maxQuestionsPerAdvisor: 3, workDir: WORK_DIR, pluginRoot: PLUGIN_ROOT})` →
   фильтр кластеров против известного →
   AskUserQuestion до 2 батчей × 4 → CONTEXT_DOSSIER. Итоги:
   `REFINED_QUERY` = RAW_QUERY, уточнённый ответами интервью;
   `USER_CONTEXT` = память (вкл. Profile) + CONTEXT_DOSSIER (досье в конце).
   Harvest пуст/упал → совет не блокируется (деградация — в протоколе).

### Advisor Table (15 советников)

Колонка Bias — известный скос советника; validator применяет её по протоколу
(Bias adjustments), а в legacy-fallback оркестратор вписывает её в строку
`Known bias:` промпта советника. «—» = скос не зафиксирован.

| Slug | Книга | Author | Prefix | Bias (известный скос) | SKILL_PATH (абсолютный) |
|------|-------|--------|--------|------------------------|--------------------------|
| cialdini | Influence | Cialdini | INF | до-алгоритмическая психология влияния — фундамент, не тактика платформ | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-cialdini` |
| hormozi | $100M Leads | Hormozi | 100ML | линза лидгена/офферов, не органический рост бренда | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-hormozi` |
| godin | This Is Marketing | Godin | TIM | — | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-godin` |
| heath | Made to Stick | Heath & Heath | MTS | — | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-heath` |
| berger | Contagious | Berger | CTG | до-алгоритмический word-of-mouth — принципы вечнозелёные, механика платформ изменилась | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-berger` |
| miller | StoryBrand | Miller | SB7 | — | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-miller` |
| kane | Hook Point | Kane | HP | self-reported цифры завышены; миф «хук = 80%» (реально ~47%) | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-kane` |
| garyvee | Day Trading Attention | GaryVee | DTA | «быть везде» — против консенсуса фокуса ранней стадии | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-garyvee` |
| kleon | Show Your Work! | Kleon | SYW | — | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-kleon` |
| traction | Traction | Weinberg & Mares | TRC | — | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-traction` |
| ferrazzi | Never Eat Alone | Ferrazzi | NEA | оффлайн-эпоха нетворкинга (2005/2014) — принципы живы, каналы изменились | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-ferrazzi` |
| known | KNOWN | Schaefer | KNW | — | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-known` |
| superfans | Superfans | Flynn | SF | — | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-superfans` |
| positioning | Obviously Awesome | Dunford | OA | — | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-positioning` |
| tactics2026 | Тактика 2026 (синтез, не книга) | ресерч платформ | T26 | волатильность: данные устаревают ~за 6 мес (см. Freshness в SKILL.md советника) | `${CLAUDE_PLUGIN_ROOT}/lenses/advisor-tactics-2026` |

## Phase B — INVOKE

```
Workflow({
  scriptPath: "${CLAUDE_PLUGIN_ROOT}/workflows/council-influence.js",
  args: {
    query: REFINED_QUERY,
    userContext: USER_CONTEXT,   // = память (вкл. Profile) + CONTEXT_DOSSIER из интервью (Phase A.5)
    advisors: <массив {slug, book, author, prefix, skillPath} из Phase A>,
    quorum: 10,
    workDir: WORK_DIR,
    pluginRoot: PLUGIN_ROOT      // ${CLAUDE_PLUGIN_ROOT} в JS НЕ подставляется — передаём значением
  }
})
```

Дождись `<task-notification>` о завершении, затем используй возвращённый объект
(`{workDir, status, advisorsAnswered, reportPath, claimLedger, verdictMeta}`). Прогресс — в `/workflows`.

Советники, скептики и validator работают одним типом воркера — субагентом
`advisor-influence:advisor-opus` (Opus, effort high). Переопределить:
`workerOpts: { model: 'opus' }` в args.

## Phase C — WRITE

Читай артефакты из `WORK_DIR` (file-mediated):

- **`status: "low-quorum"`** (ответило <10): покажи доступные `{WORK_DIR}/*.md` напрямую с warning:
  «Только {N}/15 советников ответили. Показываю доступные ответы без полного синтеза.»
- **`status: "ok"`:**
  1. `cp "{WORK_DIR}/council-verdict.md" "{OUTPUT_DIR}/{FILE_NAME}.md"`.
  2. Покажи полный вердикт; отметь ledger SUPPORTED/CONTESTED/REFUTED (из `verdictMeta.ledgerSummary`).
  3. Сообщи путь: «Вердикт сохранён: `{OUTPUT_DIR}/{FILE_NAME}.md`».
  4. `rm -rf "{WORK_DIR}"`.
  5. Пост-write (контракт `${CLAUDE_PLUGIN_ROOT}/shared/memory-write-contract.md`): допиши одну
     строку в `{RUN_LOG}` — `- YYYY-MM-DD · adv-influence · {FILE_NAME} — {итог одной строкой}`.
- **Память.** Обнови `{PROFILE}` (frontmatter product/audience/stage/channels + sessions[]).
  Секцию `## Profile` НЕ переписывать и НЕ ротировать — она курируется пользователем.
- **Follow-up.** «Детальнее по книге/принципу?» / «Следующий вопрос совету?»

## Ключевые принципы

1. **Каждый агент = 1 книга** (советник tactics2026 — 1 агент = 1 тактический синтез).
   Синтез — только в validator (без категорийной группировки).
2. **Min 10/15 для вердикта.** Меньше → ответы напрямую с warning.
3. **Per-claim cross-verify** (голосование скептиков по consensus/conflict; REFUTED отсеивается).
4. **Citations обязательны.**
5. **Интервью-харвест перед делиберацией.** Советники сдают уточняющие вопросы
   (workflow `council-question-harvest`), главная сессия дедуплицирует и интервьюирует
   пользователя один раз; все советники получают общий CONTEXT_DOSSIER, зоны «неизвестно»
   идут в вердикт секцией «Слепые зоны».

## Response Language

Язык ответа = язык запроса. Citation tags остаются на английском.
