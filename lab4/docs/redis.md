# Redis: создание и удаление ключа

## Установка Redis через Docker

Redis запущен как **отдельный Docker-контейнер**:

```bash
docker run -d --name redis -p 6379:6379 redis:7
```

- **Порт:** `6379`.
- **Имя контейнера:** `redis`.
- **Данные:** хранятся в памяти контейнера (volume не использовался — данные могут пропасть при удалении контейнера).

Проверка, что Redis работает:

```bash
docker exec -it redis redis-cli ping
```

Ожидаемый ответ: `PONG`.

## Credentials в n8n

Для подключения к Redis в n8n созданы credentials **`Redis account`**:

| Поле | Значение |
|---|---|
| **Name** | `Redis account` |
| **Host** | `host.docker.internal` |
| **Port** | `6379` |
| **Password** | *(пусто)* |
| **User** | *(пусто)* |
| **Database Number** | `0` |

**Важно:** `Host = host.docker.internal`, потому что n8n и Redis работают **в разных Docker-контейнерах**. Если указать `localhost`, то из контейнера n8n обращение пойдёт **к самому n8n**, а не к Redis. `host.docker.internal` — специальное имя, которое указывает на **хост-машину** (Mac).

## Workflow 1 — Redis Set + Get

**Файл:** `flow-09-redis.json`

**Структура:**

1. **Manual Trigger** — ручной запуск.
2. **Redis (Set)** — записать ключ `subotkovskaya` со значением `Hello from n8n`.
3. **Redis (Get)** — прочитать значение по ключу `subotkovskaya`.

**Настройки Redis (Set):**
- **Operation:** `Set`.
- **Key:** `subotkovskaya`.
- **Value:** `Hello from n8n`.

**Настройки Redis (Get):**
- **Operation:** `Get`.
- **Key:** `subotkovskaya`.
- **Property Name:** `value`.

**Результат:** OUTPUT ноды **Redis (Get)** показывает `value: Hello from n8n` — ключ **успешно записан и прочитан**.

**Скриншот:** [`08-redis-set-get.png`](../screenshots/08-redis-set-get.png)

## Workflow 2 — Redis Delete + Keys

**Файл:** `flow-10-redis.json`

**Структура:**

1. **Manual Trigger** — ручной запуск.
2. **Redis (Delete)** — удалить ключ `subotkovskaya`.
3. **Redis (Keys)** — проверить, что ключей по шаблону `subotkovskaya` больше нет.

**Настройки Redis (Delete):**
- **Operation:** `Delete`.
- **Key:** `subotkovskaya`.

**Настройки Redis (Keys):**
- **Operation:** `Keys`.
- **Key Pattern:** `subotkovskaya`.

**Результат:** OUTPUT ноды **Redis (Keys)** пустой — ключ **успешно удалён**.

**Скриншот:** [`09-redis-delete.png`](../screenshots/09-redis-delete.png)

## Как проверить вручную через Redis CLI

```bash
docker exec -it redis redis-cli GET subotkovskaya
docker exec -it redis redis-cli EXISTS subotkovskaya
```

- `GET` — вернёт значение ключа (если он есть).
- `EXISTS` — вернёт `1` (если ключ есть) или `0` (если удалён).

Пример проверки:
- После **Workflow 1** → `EXISTS subotkovskaya` вернёт `1`.
- После **Workflow 2** → `EXISTS subotkovskaya` вернёт `0`.

## Отличие Redis от встроенной n8n-БД

- **Встроенная n8n-БД** (SQLite) хранится в папке `~/.n8n` и используется для хранения workflows и credentials.
- **Redis** — это **внешнее key-value хранилище**, работающее как **отдельный сервис**. Оно полезно для:
  - кэширования,
  - хранения сессий,
  - pub/sub (публикация/подписка),
  - быстрого доступа к данным по ключу.

В лабораторной работе Redis использован для демонстрации работы n8n с **внешними хранилищами данных**.
