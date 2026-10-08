# Каталог Context Engineering Pack

Pack — рабочий набор документов, по которому можно понять процесс, поставить задачу агенту, дать источники, ограничить риск, подключить tools, проверить результат, передать работу и обслуживать контур (гл. 19). Pack — источник истины для исполняемого агента: агент ссылается на файлы, а не дублирует их.

Исключены сознательно (это артефакты о человеке и рынке, а не об агенте): LLM Basics Card, Market Evidence Map, Profession Realism Checklist, Skill Gap Map, Next 30 Days Plan.

## Ядро (создаётся всегда)

| Файл | Артефакты курса | Шаблон | Глава |
|---|---|---|---|
| `01-task-brief.md` | Agent Task Brief | `templates/task-brief.md` | 3 |
| `02-role-card.md` | Agent Role Card (+ Capability Card) | `templates/role-card.md` | 4–5 |
| `03-source-of-truth.md` | Source of Truth Map | `templates/source-of-truth.md` | 7 |
| `04-tool-contracts.md` | Tool Contract ×N + Tool Risk Checklist + Tool Test Set | `templates/tool-contract.md` | 10 |
| `05-workflow.md` | Agent Workflow Map + Workflow Stop Rules + Checkpoints + Workflow Test Set | `templates/workflow.md` | 11 |
| `06-approval-matrix.md` | Action Approval Matrix | `templates/approval-matrix.md` | 15 |
| `07-eval-checklist.md` | Eval Checklist + Result Rubric + Escalation Rule | `templates/eval.md` | 14 |
| `08-golden-tasks.md` | Golden Task Set | `templates/golden-tasks.md` | 14 |

## Модули (включаются триггерами гриллинга)

| Модуль | Триггер | Файл(ы) | Шаблон | Глава |
|---|---|---|---|---|
| Domain/Process | P5 | `10-domain-context.md` (Domain Context Map + Process Vocabulary), `11-business-process.md` (Business Process Map + Decision and Exception Map + Agent Fit) | `templates/domain-process.md` | 16 |
| Knowledge/Retrieval | K0 | `12-knowledge-retrieval.md` (Knowledge Source Map + Retrieval Plan + Query Test Set) | `templates/knowledge-retrieval.md` | 7–8 |
| Memory/State | M0 | `13-memory-state.md` (Memory Policy + State Map + Memory Test Set) | `templates/memory-state.md` | 9 |
| Context Window | W0 | `14-context-window.md` (Context Window Strategy + Compression/Isolation Rules) | `templates/context-window.md` | 13 |
| Data/Governance | G0 | `15-data-governance.md` (Context Risk Checklist + Data Boundary Map + Prompt Injection Rule + Governance Rule Card) | `templates/data-governance.md` | 15 |
| Handoff/Multi-agent | H0 | `16-handoff.md` (Multi-agent Handoff Map + Role Split Matrix + Handoff Packet + Orchestrator/Worker Contract + Return Report) | `templates/handoff.md` | 17 |
| Maintenance | X0 | `17-maintenance.md` (Context Maintenance Loop + Observation Plan + Signal Dashboard + Maintenance Calendar) | `templates/maintenance.md` | 18 |

Номера модулей фиксированы: у отсутствующего модуля номер пропускается. Так ссылки между файлами не ломаются при включении модуля позже (Update).

## Служебные файлы

| Файл | Когда | Содержание |
|---|---|---|
| `00-README.md` | всегда | Inventory Pack + Context Inventory (см. ниже) |
| `_grilling-log.md` | с начала гриллинга | Все решения, статус, модули (`question-tree.md` → «Лог») |
| `capability-check.md` | валидация | Capability Check Card + результат статической проверки |
| `validation-report.md` | валидация | Прогон Golden Tasks, оценки по рубрике, выводы |
| `failure-log.md` | первый провал | Failure Log / Incident Note (шаблон в `templates/eval.md`) |
| `context-update-log.md` | режим Update | Журнал изменений контекста (шаблон в `templates/maintenance.md`) |

## `00-README.md`

```markdown
# <Название агента> — Context Engineering Pack

**Процесс:** <одно предложение по формуле «Когда…, нужно…, чтобы…»>
**Назначение:** <из Role Card>
**Исполняемый агент:** <путь> (<subagent | skill | orchestrator>), модель <…>
**Scope:** <project | global> · **Владелец агента:** <…> · **Создан:** <дата> · **Версия Pack:** 1

## Inventory

| # | Артефакт | Файл | Статус |
|---|---|---|---|
| 01 | Agent Task Brief | [01-task-brief.md](01-task-brief.md) | ✅ есть |
| 05 | Workflow Map | [05-workflow.md](05-workflow.md) | 🟡 доработать: <что> |
| 12 | Knowledge/Retrieval | — | ⚪ модуль не нужен: <почему> |

Статусы: ✅ есть · 🟡 доработать (есть `⚠ Не определено`) · ⚪ модуль не нужен · ❌ нет (блокер).

## Context Inventory (гл. 6)

| Слой | Где определён | Кратко |
|---|---|---|
| Task | 01 | … |
| Role | 02 | … |
| Instructions | 05, агент | … |
| Sources | 03, 12 | главный: … |
| History | — | что из истории не считать правилом: … |
| Memory | 13 / нет | … |
| Tools | 04 | … |
| State | 13 / Return Report | … |
| Constraints | 02, 06, 15 | … |
| Verification | 07, 08 | … |
| Stop rules | 05 | … |

## Критерий готовности контура
- [ ] Capability Check без блокеров
- [ ] Golden Tasks: все stop/approval-задачи pass, остальные ≥ 80%
- [ ] Нет 🟡 в ядре
```

## Правила заполнения

1. Каждое поле шаблона заполняется только из `_grilling-log.md` или из найденных фактов с указанием источника. Пусто — `⚠ Не определено: <что нужно решить>`.
2. Примеры из шаблонов (агент разбора заявок) — это иллюстрация уровня детализации. В Pack их не копировать.
3. Перекрёстные ссылки — относительные: `см. [05-workflow.md](05-workflow.md#stop-rules)`.
4. Без секретов, токенов и персональных данных. Источник с такими данными упоминай по названию, версии и пути, содержимое не копируй (гл. 15).
5. Каждый файл начинается с шапки: назначение артефакта одной строкой и `Связано: <номера файлов>`.
