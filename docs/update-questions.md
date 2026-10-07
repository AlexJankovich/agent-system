# Отчёт о согласованности документации

## Статус проверки
Выполнена проверка согласованности файлов `@AGENTS.md`, `@README.md` с файлами внутри папок `.opencode/agents/` и `.opencode/skills/`.

**Важное замечание:** В промпте был приложен **другой вариант AGENTS.md** (с OpenSpec-ссылками, `packages/contracts`, `openspec/wiki/`), которого **нет в актуальной файловой системе**. Ниже отчёт по **реальным файлам на диске**.

---

## Реальное состояние файлов (проверено на диске)

### AGENTS.md (217 строк) — **чистый**, без OpenSpec-путеводителей
### README.md (154 строки) — актуален
### .opencode/skills/ — 17 скиллов
### .opencode/agents/ — 7 агентов
### .opencode/commands/ — 3 команды

---

## Критические противоречия (реальные, в текущих файлах)

### 1. README.md: `agent-loop` отсутствует в списке «Основной цикл»
**Файл:** `README.md`  
**Строки:** 31–46 (раздел «Основной цикл»)  
**Детали:** Перечислены 10 скиллов: `spec-driven-development`, `planning-and-task-breakdown`, `incremental-implementation`, `tdd`, `debugging-and-error-recovery`, `code-review`, `git-conventions`, `pull-request`, `ci-cd-and-automation`, `frontend-ui-engineering`.  
**Отсутствует:** `agent-loop` — скилл оркестрации полного цикла DEFINE→SHIP.  
**Примечание:** В AGENTS.md (стр. 80–82) `agent-loop` назван предпочтительным для многослайсовых фич. В README.md он вынесен в отдельный раздел «Оркестрация» (стр. 48–51), но не в «Основной цикл».

---

### 2. README.md: `openspec-workflow` отсутствует в списке периферийных скиллов
**Файл:** `README.md`  
**Строки:** 53–59 (раздел «Периферийные»)  
**Детали:** Перечислены: `humanizer`, `dnote`, `qa`.  
**Отсутствует:** `openspec-workflow` — который есть в `.opencode/skills/` (autoinvoke: false).  
**AGENTS.md (стр. 48–53)** периферийными называет: `qa`, `humanizer`, `dnote` — тоже без `openspec-workflow`.

---

### 3. AGENTS.md: периферийные скиллы — неполный список
**Файл:** `AGENTS.md`  
**Строки:** 48–53  
**Детали:** Упомянуты только `qa`, `humanizer`, `dnote`.  
**Отсутствуют:** `openspec-workflow` (есть в репозитории, autoinvoke: false).

---

### 4. oracle.md: опечатка в заголовке формата вывода
**Файл:** `.opencode/agents/oracle.md`  
**Строка:** 67  
**Текст:** `## nherited decisions:`  
**Должно быть:** `## Inherited decisions:` (или `## Унаследованные решения:` для единообразия).

---

## Некритические несоответствия

### 5. Порядок периферийных скиллов отличается
- **AGENTS.md (стр. 50–53):** `qa`, `humanizer`, `dnote`
- **README.md (стр. 56–58):** `humanizer`, `dnote`, `qa`  
Состав совпадает (без `openspec-workflow`), порядок разный.

### 6. README.md: в «Справочные материалы» (стр. 87–91) перечислены только 3 файла
Упомянуты: `security-checklist.md`, `performance-checklist.md`, `git-conventions/SKILL.md`.  
Не упомянут: `smell-baseline.md` (он есть в `code-review/references/` и используется в `code-review/SKILL.md`).

---

## Что соответствует полностью ✅

| Проверка | Статус |
|----------|--------|
| 17 скиллов в `.opencode/skills/` с SKILL.md | ✅ |
| 7 агентов в `.opencode/agents/` с правильными permissions | ✅ |
| 3 команды в `.opencode/commands/` | ✅ |
| AGENTS.md: оркестрация `build → subagent` | ✅ |
| AGENTS.md: Lifecycle Mapping (DEFINE→SHIP) | ✅ |
| AGENTS.md: список 7 агентов в разделе «Оркестрация» (стр. 104–130) | ✅ |
| AGENTS.md: 3 команды (стр. 120–123) | ✅ |
| README.md: структура `.opencode/` (стр. 14–24) | ✅ |
| README.md: список 7 агентов (стр. 62–70) | ✅ |
| README.md: 3 команды (стр. 74–81) | ✅ |
| pull-request/SKILL.md: загрузка git-conventions первым шагом | ✅ |
| openspec-workflow/SKILL.md: autoinvoke: false, предупреждение | ✅ |
| agent-loop/SKILL.md: полный цикл с 2 гейтами | ✅ |
| kebab-case naming для всех скиллов/агентов/команд | ✅ |

---

## Рекомендации по исправлению

### Высокий приоритет

1. **README.md (стр. 31–46):** Добавить `agent-loop` в список «Основной цикл» (после `frontend-ui-engineering` или в начало как оркестратор).

2. **README.md (стр. 53–59) + AGENTS.md (стр. 48–53):** Добавить `openspec-workflow` в периферийные скиллы с пометкой `(autoinvoke: false, справочный)`.

### Средний приоритет

3. **README.md (стр. 87–91):** Добавить `smell-baseline.md` в «Справочные материалы».

4. **AGENTS.md (стр. 48–53) / README.md (стр. 56–58):** Унифицировать порядок периферийных скиллов (например, алфавитный: `dnote`, `humanizer`, `openspec-workflow`, `qa`).

### Низкий приоритет

5. **.opencode/agents/oracle.md (стр. 67):** Исправить `## nherited decisions:` → `## Inherited decisions:` (или `## Унаследованные решения:`).

---

*Отчёт сгенерирован: Wed Oct 07 2026*  
*Проверены актуальные файлы на диске: AGENTS.md (217 стр.), README.md (154 стр.), .opencode/agents/*.md, .opencode/skills/*/SKILL.md, .opencode/commands/*.md*  
*Всего скиллов: 17, агентов: 7, команд: 3*
