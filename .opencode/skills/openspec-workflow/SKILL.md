---
name: openspec-workflow
description: Справочник по OpenSpec-воркфлоу (DEFINE-фаза). Используй, только если пользователь явно просит применить OpenSpec или если в репозитории появились директории openspec/specs или openspec/changes. Не вызывай автоматически — OpenSpec-скиллы могут быть недоступны в текущей сборке OpenCode.
metadata:
  opencode/autoinvoke: false
---

# OpenSpec Workflow (справочный)

Этот скилл документирует OpenSpec-воркфлоу, но **не является частью
основного цикла**. Вызывай его только по явному запросу пользователя.

## Когда использовать

- Пользователь прямо сказал: «примени OpenSpec», «сделай openspec-proposal»,
  «заархивируй изменение».
- В репозитории есть директории `openspec/specs/` или `openspec/changes/`.
- Требуется формальное управление изменениями (change management).

Если ничего из перечисленного не выполнено — **не загружай этот скилл**.

## Ограничение

OpenSpec-скиллы (`openspec-propose`, `openspec-apply-change`,
`openspec-archive-change`, `openspec-explore`, `openspec-sync-specs`,
`openspec-update-change`) могут быть недоступны в текущей сборке
OpenCode. Если при попытке загрузки скилл не найден — сообщи об этом
пользователю и предложи альтернативу: ручное согласование требований
через `context-builder` + `oracle` (см. основной цикл в `agent-loop`).

## Справочный жизненный цикл OpenSpec

| Фаза | Команда / скилл | Результат |
|---|---|---|
| DEFINE | `/openspec-propose` | `openspec/changes/<change>/proposal.md`, `tasks.md`, delta-спека |
| BUILD | `/openspec-apply-change` | Реализация по задачам изменения |
| VERIFY | `/openspec-sync-specs` | Синхронизация дельт со спецификацией |
| ARCHIVE | `/openspec-archive-change` | Перенос изменения из `changes/` в `specs/` |
| EXPLORE | `/openspec-explore` | Разведка существующих спецификаций |

## Правила

- Не начинай реализацию до утверждения предложения.
- Одно изменение — одна capability (одна папка под `openspec/specs/`).
- Если изменение затрагивает контракты — сначала изменение контракта,
  затем — модуль.
- Никогда не запускай модульное изменение до принятия контракта.