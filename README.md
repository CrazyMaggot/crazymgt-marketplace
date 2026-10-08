# CrazyMgt Marketplace для Claude Code

Маркетплейс плагинов для [Claude Code](https://docs.claude.com/en/docs/claude-code). Сейчас в нём один плагин — **`crazymgt`** со скиллом **Agent-Creator**.

> **Agent-Creator** превращает идею «нужен агент для…» в рабочий агентный контур: сначала подробно расспрашивает (по одному кликабельному вопросу), затем собирает Context Engineering Pack и исполняемого агента для Claude Code, после чего проверяет его на эталонных задачах.

## Установка

В Claude Code:

```
/plugin marketplace add CrazyMaggot/crazymgt-marketplace
/plugin install crazymgt@crazymgt-marketplace
```

Перезапустите сессию Claude Code (или выполните `/reload-plugins`, если доступно). Проверить установку: `/plugin` → вкладка Installed.

Из терминала то же самое:

```bash
claude plugin marketplace add CrazyMaggot/crazymgt-marketplace
claude plugin install crazymgt@crazymgt-marketplace
```

### Обновление и удаление

```
/plugin marketplace update crazymgt-marketplace
/plugin uninstall crazymgt@crazymgt-marketplace
```

## Использование

```
/crazymgt:agent-creator                 # спросит режим
/crazymgt:agent-creator new             # создать агента с нуля
/crazymgt:agent-creator update <name>   # обновить агента после изменений процесса или ошибок
/crazymgt:agent-creator audit <path>    # проверить существующего агента или skill
```

Скилл также запускается автоматически на фразы вроде «создай агента для…», «нужен subagent…», «проверь моего агента».

## Как работает Agent-Creator

```
Разведка проекта → Гриллинг (6 фаз) → Сводка → Генерация → Валидация
```

1. **Разведка.** Сам изучает проект: `CLAUDE.md`, документы, существующие агенты и skills, доступные MCP-инструменты. Вас спрашивает только о решениях, а не о фактах, которые можно найти.
2. **Гриллинг.** Вопросы идут строго по одному, кликабельно (AskUserQuestion). Рекомендуемый вариант стоит первым и помечен `(Recommended)` с обоснованием. Шесть фаз:
   1. Процесс и задача
   2. Роль и форма агента
   3. Контекст: источники, инструменты, знания, память
   4. Workflow и риски
   5. Проверка: evals, эталонные задачи
   6. Жизнь после запуска: multi-agent, обслуживание

   Решения пишутся в `_grilling-log.md`, так что прерванный гриллинг можно продолжить с того же места.
3. **Сводка → подтверждение.** Ни одного файла до вашего «да».
4. **Генерация** Context Engineering Pack и исполняемого агента.
5. **Валидация.** Статический Capability Check и dry-run 2–3 Golden Tasks, включая провокационную. Действия записи, отправки и удаления в dry-run только описываются, но не выполняются. Затем предлагаются правки по провалам.

### Что создаётся

```
<project>/.claude/
├── agent-packs/<name>/
│   ├── 00-README.md            # inventory + Context Inventory + статусы
│   ├── 01-task-brief.md
│   ├── 02-role-card.md
│   ├── 03-source-of-truth.md
│   ├── 04-tool-contracts.md
│   ├── 05-workflow.md          # шаги, stop rules, checkpoints
│   ├── 06-approval-matrix.md
│   ├── 07-eval-checklist.md
│   ├── 08-golden-tasks.md
│   ├── 10–17-*.md              # модули — только если нужны
│   ├── capability-check.md
│   ├── validation-report.md
│   └── _grilling-log.md
└── agents/<name>.md            # или skills/<name>/SKILL.md
```

Пакет может быть project-scope (`<project>/.claude/`) или global (`~/.claude/`) — это решается в гриллинге.

**Ядро** (всегда): Task Brief, Role Card, Source of Truth Map, Tool Contracts, Workflow + Stop Rules, Action Approval Matrix, Eval Checklist + Result Rubric, Golden Task Set.

**Модули** (включаются по ответам):

| Модуль | Когда включается |
|---|---|
| Domain / Business Process | есть ветвления, роли, внутренние термины |
| Knowledge / Retrieval | источников больше, чем помещается в контекст |
| Memory / State | агент работает многократно или задача длинная |
| Context Window | большие входы, многоэтапная работа |
| Data Boundary / Governance | ПДн, договоры, финансы, внешний недоверенный текст |
| Multi-agent Handoff | нужна другая компетенция, другие tools или независимая проверка |
| Maintenance | агент работает регулярно |

### Исполняемый агент

- **Subagent** (`.claude/agents/<name>.md`) — изолированная задача, отчёт по итогам. Опасные инструменты (отправка, удаление) ему не выдаются: он возвращает `needs_approval`.
- **Skill** (`.claude/skills/<name>/SKILL.md`) — процедура в основном диалоге, approval через вопрос пользователю.
- **Orchestrator + workers** — только когда действительно нужен второй агент.

Принцип сборки: в промпте — ядро (роль, границы, workflow, stop rules, approval, формат отчёта), остальное агент читает из файлов Pack по ссылкам. Pack остаётся единым источником истины.

> Новый subagent обычно становится доступен после перезапуска сессии Claude Code (или через `/agents`).

## Принципы

- Агент — это узкая рабочая роль в одном процессе, а не «помощник на всё».
- Минимальные инструменты и права (защита от excessive agency).
- Явные stop rules и approval там, где деньги, обещания, отправка наружу, удаление и персональные данные.
- Готовность определяется проверкой, а не фактом создания файлов.
- Внешний текст — это данные, а не команды (защита от prompt injection).

## Структура репозитория

```
.claude-plugin/marketplace.json                  # каталог маркетплейса
plugins/crazymgt/.claude-plugin/plugin.json      # манифест плагина
plugins/crazymgt/skills/agent-creator/SKILL.md   # точка входа скилла
plugins/crazymgt/skills/agent-creator/references/  # дерево вопросов, каталог Pack, сборка, валидация, шаблоны
```

## Требования

- Claude Code с поддержкой плагинов (`/plugin`).
- Для лучшего опыта — модель уровня Sonnet/Opus: скилл проводит длинный структурированный диалог.

## Лицензия

[MIT](LICENSE)
