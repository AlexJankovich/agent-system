> Создай change для capability `contracts`. Это foundational change: он определяет типы и схемы, от которых зависят все остальные модули.
>
> В `openspec/specs/contracts/spec.md` опиши следующие разделы.
>
> ### 1. Envelope
>
> Все межпроцессные сообщения — это `Envelope`. Поля:
>
> | Поле | Тип | Обязательное | Описание |
> |---|---|---|---|
> | `id` | `string` (UUID v4) | да | Уникальный идентификатор сообщения |
> | `correlationId` | `string` (UUID v4) | нет | ID запроса, на который отвечают. Для ответов и ошибок — обязателен |
> | `timestamp` | `number` (ms since epoch) | да | Время создания |
> | `from` | `string` | да | Имя процесса-отправителя |
> | `to` | `string` | да | Имя процесса-получателя, либо `"core"` |
> | `type` | `string` | да | Тип сообщения, см. раздел 2 |
> | `version` | `"1.0"` | да | Версия протокола |
> | `payload` | `unknown` | да | Данные, схема которых зависит от `type` |
>
> Опиши требования: envelope MUST быть сериализуем в одну строку JSON; MUST заканчиваться `\n`; получатель MUST отклонять сообщения с неизвестной `version`.
>
> ### 2. Message types
>
> Перечисли все типы сообщений с указанием направления и схемы payload.
>
> - `chat.new` (adapter → core): `{ chatId: number, conversationId: string, text: string, userId: number }`
> - `task.create` (adapter → core): `{ goal: string, chatId: number, userId: number, conversationId?: string }`
> - `llm.request` (core → llm): `{ requestId: string, prompt: string, systemPrompt?: string }`
> - `llm.response` (llm → core): `{ requestId: string, text: string, model: string }`
> - `error` (any → any): `{ code: string, message: string, details?: Record<string, unknown>, retryable: boolean }`
> - `event.system.heartbeat` (any → core): `{ processName: string, uptimeMs: number }`
>
> ### 3. Error model
>
> Коды ошибок (enum). Для каждого кода укажи: категорию (`transient` / `permanent` / `user_error`), retryable, что показывать пользователю.
>
> - `LLM_TIMEOUT` — transient, retryable, «Сервис временно недоступен»
> - `LLM_EMPTY_RESPONSE` — transient, retryable, «Ответ не был сформирован»
> - `LLM_PROVIDER_ERROR` — permanent, not retryable, «Ошибка при обращении к модели»
> - `CHANNEL_SEND_FAILED` — transient, retryable, «Не удалось отправить сообщение»
> - `UNKNOWN_TARGET` — permanent, not retryable, «Внутренняя ошибка маршрутизации»
> - `INVALID_MESSAGE` — permanent, not retryable, лог only
>
> ### 4. Config schema
>
> Опиши форму конфигурации (в терминах TypeScript-типа или JSON Schema). Обязательные поля:
>
> - `llm.model: string`
> - `llm.timeoutMs: number` (default 30000)
> - `llm.systemPrompt?: string`
> - `channel.type: "telegram"`
> - `channel.token: string`
> - `logging.level: "debug" | "info" | "warn" | "error"` (default `"info"`)
> - `maxConcurrentTasks: number` (default 1)
>
> Укажи, что конфиг загружается один раз при старте, валидируется по схеме, при ошибке процесс завершается с кодом 1.
>
> ### 5. Runtime contracts (TypeScript)
>
> Приведи TypeScript-типы, которые будут реализованы в `packages/contracts`:
>
> ```ts
> export type Envelope<T = unknown> = {
>   id: string;
>   correlationId?: string;
>   timestamp: number;
>   from: string;
>   to: string;
>   type: string;
>   version: "1.0";
>   payload: T;
> };
>
> export type ErrorPayload = {
>   code: ErrorCode;
>   message: string;
>   details?: Record<string, unknown>;
>   retryable: boolean;
> };
>
> export type ErrorCode = /* union из раздела 3 */;
> ```
>
> Требования к `packages/contracts`:
> - только типы и Zod-схемы (если выбран Zod) — без рантайм-логики;
> - ноль runtime-зависимостей, кроме `zod` (если используется);
> - экспортирует схему `envelopeSchema` для валидации на входе.
>
> ### Границы
>
> - Не описывай поведение Bot Core, LLM Adapter, Adapter Layer. Только контракт.
> - Не пиши код реализации. Только спека.
> - Ссылайся на `docs/README.md` §4 как на источник терминов.