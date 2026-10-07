# Настройка Model Context Protocol (MCP) для OpenCode v2

**Model Context Protocol (MCP)** — открытый стандарт, позволяющий
ИИ-агентам безопасно подключаться к внешним источникам данных: базам
данных, API, локальным файловым системам и сторонним сервисам
(GitHub, Slack и др.).

## Как это устроено

OpenCode запускает MCP-серверы как дочерние процессы, читая их описания
из `opencode.json`. Агенту становятся доступны инструменты, которые
предоставляет каждый сервер.

```
┌─────────────┐   MCP Protocol   ┌─────────────┐
│  OpenCode   │ ───────────────> │ MCP Server  │ ──> [БД]
│  (агент)    │ <─────────────── │ (Postgres)  │ <── [ФС]
└─────────────┘                  └─────────────┘
```

## Расположение конфигурации

- **Проект:** `./opencode.json` в корне репозитория.
- **Глобально:** `~/.config/opencode/opencode.json`.

Проектный конфиг имеет приоритет над глобальным. Оба файла могут
сосуществовать — проектный переопределяет нужные ключи.

## Структура `opencode.json` для MCP

MCP-серверы описываются в ключе `mcp`. Каждый сервер имеет `type`
(`local` для локально запускаемых процессов, `remote` для удалённых)
и `command` (массив аргументов).

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "postgres": {
      "type": "local",
      "command": [
        "npx", "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://username:password@localhost:5432/my_database"
      ],
      "enabled": true
    },
    "github": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-github"],
      "environment": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "{env:GITHUB_TOKEN}"
      },
      "enabled": true
    },
    "slack": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-slack"],
      "environment": {
        "SLACK_BOT_TOKEN": "{env:SLACK_BOT_TOKEN}"
      },
      "enabled": false
    }
  }
}
```

> [!IMPORTANT]
> - Поле называется `environment`, не `env`.
> - Поле `command` — **массив**, а не строка.
> - Секреты передавай через `{env:VAR_NAME}` и держи значения в
>   переменных окружения, не в конфиге.
> - Если `npx` не находится (ошибка `spawn npx ENOENT`), укажи полный
>   путь: `"/opt/homebrew/bin/npx"` (macOS) или `"C:\\Program Files\\nodejs\\npx.cmd"` (Windows).

## Детальная настройка популярных серверов

### PostgreSQL

Даёт агенту возможность читать схему таблиц и выполнять SQL-запросы.

- **Установка:** автоматически через `npx`, либо
  `npm install -g @modelcontextprotocol/server-postgres`.
- **Строка подключения:** URI с логином, паролем, хостом, портом, БД.
- **Рекомендация:** используй read-only-пользователя для БД, чтобы
  агент физически не мог выполнить `UPDATE`/`DELETE`.

### GitHub

Позволяет читать issues и PR, оставлять комментарии, работать с
releases и workflow runs. Заменяет прежний GitLab-сервер.

- **Токен:** Personal Access Token с scopes `repo`, `read:org`,
  `workflow`.
- **Сервер:** `@modelcontextprotocol/server-github`.
- Используется скиллом `qa` (создание issues) и `pull-request`
  (проверка статуса PR).

### Slack

Используется для уведомлений об упавших сборках или запросах на ревью.

- Требует создания Slack App в воркспейсе.
- Bot Scopes: `chat:write`, `channels:read`.

## Инструкции для агента

Когда ты используешь MCP-инструменты:

- [ ] **Не навреди.** Модифицирующие действия (`UPDATE`, `DELETE`,
      `git push`, отправка сообщений) — только с явного одобрения
      человека.
- [ ] **Сначала исследуй схему.** Перед SQL-запросами читай схему
      таблиц, чтобы знать точные имена колонок и типы.
- [ ] **Ограничивай лимиты.** Всегда ставь `LIMIT` при выборках и
      ограничивай размер выдачи API.
- [ ] **Логируй действия.** Если отправлено уведомление или изменён
      статус задачи — сообщи об этом в ответе.

## Решение проблем

1. **`spawn npx ENOENT`**
   - Причина: клиент не находит `npx` в `PATH`.
   - Решение: замени `"npx"` на абсолютный путь. Узнать:
     `which npx` (macOS/Linux), `where npx` (Windows).

2. **Ошибка 401/403 от MCP-сервера**
   - Причина: невалидный или истёкший токен.
   - Решение: проверь переменные окружения, на которые ссылается
     `environment`. Убедись, что токен GitHub не истёк и имеет нужные
     scopes.

3. **Таймауты БД**
   - Причина: БД не запущена или блокирует входящие соединения.
   - Решение: проверь доступность хоста, firewall, права пользователя.

## См. также

- [OpenCode MCP documentation](https://opencode.ai/docs/mcp)
- `opencode.json` в корне репозитория — пример конфигурации.