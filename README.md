# Engineering Skills & AI Agents

Готовая библиотека скиллов, ролей агентов, команд оркестрации и
справочных чек-листов для интеграции ИИ-ассистентов в процесс
веб-разработки. Собрана для **OpenCode v2** с публикацией через
**GitHub**.

---

## Архитектура

Проект организован в три компонуемых слоя плюс справочные материалы:

```
.opencode/
├── skills/          # Как?    — workflow с шагами и критериями выхода
├── agents/          # Кто?    — персоны с перспективой и форматом вывода
├── commands/        # Когда?  — пользовательские точки входа оркестрации
└── opencode.jsonc   # Конфигурация OpenCode (MCP, permissions)

docs/                # Setup и справочная документация
AGENTS.md            # Конституция агентов
README.md            # Этот файл
```

### Скиллы (`.opencode/skills/`)

Специализированные директории с `SKILL.md` и опциональными `scripts/`
или `references/`. Каждый скилл описывает пошаговый процесс:

**Основной цикл:**

- `spec-driven-development` — спецификация перед кодом для крупных
  фич, новых проектов, неясных требований
- `planning-and-task-breakdown` — декомпозиция на верифицируемые задачи
- `incremental-implementation` — построение по одному вертикальному слайсу
- `tdd` — цикл red → green с `tests.md` и `mocking.md`
- `debugging-and-error-recovery` — систематический триаж первопричин
- `code-review` — двухосевое ревью (Standards + Spec) с
  `security-checklist.md`, `performance-checklist.md` и
  `smell-baseline.md`
- `git-conventions` — именование веток, Conventional Commits, шаблоны PR
- `pull-request` — автоматизация branch → commit → push → `gh pr create`
- `ci-cd-and-automation` — quality gates, GitHub Actions, стратегии
  деплоя
- `frontend-ui-engineering` — production-quality UI с a11y

**Оркестрация:**

- `agent-loop` — полный цикл DEFINE → SHIP с двумя точками одобрения
  пользователем

**Периферийные** — вне основного цикла, вызываются только по явному
запросу:

- `humanizer` — удаление ИИ-паттернов из текста
- `dnote` — личные заметки через CLI
- `qa` — разговорный приём багов → GitHub issues

### Агенты (`.opencode/agents/`)

Специализированные роли с разной глубиной мышления и правами:

- `build` — оркестратор (primary)
- `worker` — реализация и разработка
- `reviewer` — ревью кода и планов
- `researcher` — сбор внешней информации
- `scout` — быстрая разведка кодовой базы
- `oracle` — согласованность решений и консультации по архитектуре
- `context-builder` — сборка контекста для цепочек агентов

### Команды (`.opencode/commands/`)

Сценарии оркестрации для многоагентной работы:

- `/review-loop` — разработчик правит замечания ревьюера до полной
  чистоты
- `/parallel-review` — параллельные ревьюеры под разными углами, отчёты
  сливаются
- `/parallel-research` — параллельные researcher + scout, находки
  сливаются

### Справочные материалы

Чек-листы и соглашения для контроля качества:

- `.opencode/skills/code-review/references/security-checklist.md` —
  OWASP, auth, валидация ввода
- `.opencode/skills/code-review/references/performance-checklist.md` —
  Core Web Vitals, фронтенд и бэкенд
- `.opencode/skills/git-conventions/SKILL.md` — ветки, коммиты, PR
- - `.opencode/skills/code-review/references/smell-baseline.md` — Fowler-овский
  smell-baseline для оси Standards

---

## Метод работы: Skill-Driven Execution

Основной принцип репозитория — **skill-driven execution**:

1. **ИИ не импровизирует.** Когда возникает задача (например, «напиши
   аутентификацию»), ИИ-ассистент должен сначала найти подходящий скилл
   в `.opencode/skills/` и строго следовать его шагам.
2. **Ты-форма.** Все промпты и инструкции адресуют ИИ-агента в
   **единственном числе** («ты»). Это помогает ИИ точнее сопоставлять
   себя с исполняемой ролью.

### Жизненный цикл

- DEFINE → `spec-driven-development` (для крупных задач) или
  `context-builder` (сбор контекста)
- PLAN → `planning-and-task-breakdown`
- BUILD → `incremental-implementation` + `tdd`
- VERIFY → `debugging-and-error-recovery`
- REVIEW → `code-review`
- SHIP → `pull-request` + `ci-cd-and-automation`

Для многослайсовых фич используй скилл `agent-loop` — он проводит
весь цикл с двумя точками одобрения.

---

## Быстрый старт

1. Прочитай [AGENTS.md](./AGENTS.md) — конституция агентов, карта
   «intent → skill» и жизненный цикл.
2. Настрой [Model Context Protocol (MCP)](./docs/mcp-setup.md), чтобы
   дать ИИ доступ к GitHub, базам данных или другим внешним сервисам.
3. Проверь `.opencode/opencode.jsonc` — конфигурация MCP-серверов и
   глобальных permissions.
4. Убедись, что установлен OpenCode v2.

### Требования

- OpenCode v2
- `gh` CLI установлен и аутентифицирован
- Node.js доступен (для MCP-серверов через `npx`)

---

## Соглашения репозитория

- **Именование:** kebab-case для скиллов, агентов, команд и скриптов.
- **SKILL.md до 500 строк** — детали в `references/`.
- **Progressive disclosure** — файлы `references/` загружаются только
  при необходимости.
- **Ссылки на один уровень вглубь** — из `SKILL.md` прямо в
  `references/`.
- **Предпочитай скрипты inline-коду** — запуск скрипта не потребляет
  контекст.

---

## Лицензия

MIT