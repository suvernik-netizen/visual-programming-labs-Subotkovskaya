# Отчёт по лабораторной работе №4 — n8n

## Краткое описание

В ходе лабораторной работы №4 была освоена **n8n** — low-code платформа автоматизации. n8n установлен через **Docker** с двумя volume: для настроек (`~/.n8n`) и данных (`~/n8n-data`). Создано **8 workflow**, демонстрирующих базовые и продвинутые ноды: от простого Manual Trigger → Set → Code до интеграции с **Redis** через отдельный Docker-контейнер.

Все workflows сохранены в формате JSON в папке `lab4/flows/`, скриншоты — в `lab4/screenshots/`. Документация по Redis — в `lab4/docs/redis.md`.

## Способ установки и версии

**Способ установки:** Docker.

**Команда запуска n8n:**

```bash
docker run -d --name n8n -p 5678:5678 \
  -e N8N_SECURE_COOKIE=false \
  -v ~/.n8n:/home/node/.n8n \
  -v ~/n8n-data:/data \
  n8nio/n8n
```

Флаг `N8N_SECURE_COOKIE=false` нужен, чтобы Safari мог работать с n8n по HTTP (без HTTPS).

**Команда запуска Redis:**

```bash
docker run -d --name redis -p 6379:6379 redis:7
```

**Версии:**
- n8n: `n8nio/n8n:latest` (Docker-образ).
- Redis: `redis:7`.

## Список workflow

| # | Название | Файл | Скриншот |
|---|---|---|---|
| 2.1 | Manual Trigger → Set → Code | [flow-01-manual-set.json](../flows/flow-01-manual-set.json) | [02-manual-set.png](../screenshots/02-manual-set.png) |
| 2.2 | Manual Trigger → Code → NoOp | [flow-02-code.json](../flows/flow-02-code.json) | [03-code.png](../screenshots/03-code.png) |
| 2.3 | Manual Trigger → Code → IF → NoOp (true) / NoOp (false) | [flow-03-if.json](../flows/flow-03-if.json) | [04-if.png](../screenshots/04-if.png) |
| 2.4 | Manual Trigger → Set → Code (с timestamp) | [flow-04-set.json](../flows/flow-04-set.json) | [05-set.png](../screenshots/05-set.png) |
| 2.5 | Manual Trigger → HTTP Request → Code | [flow-05-http.json](../flows/flow-05-http.json) | [06-http.png](../screenshots/06-http.png) |
| 2.6 | Schedule Trigger → Code (каждые 5 мин) | [flow-06-schedule.json](../flows/flow-06-schedule.json) | [07-schedule.png](../screenshots/07-schedule.png) |
| 3.1 | Manual Trigger → Redis Set → Redis Get | [flow-09-redis.json](../flows/flow-09-redis.json) | [08-redis-set-get.png](../screenshots/08-redis-set-get.png) |
| 3.2 | Manual Trigger → Redis Delete → Redis Keys | [flow-10-redis.json](../flows/flow-10-redis.json) | [09-redis-delete.png](../screenshots/09-redis-delete.png) |

## Освоенные ноды

### Триггеры
- **Manual Trigger** — ручной запуск workflow кнопкой.
- **Schedule Trigger** — запуск по расписанию (интервал, cron, дни/часы/минуты).

### Обработка данных
- **Set (Edit Fields)** — формирование полей: строки, числа, даты, выражения (`{{ $now }}`).
- **Code (JavaScript)** — произвольная логика на JS: `let`/`const`, `if/else`, цикл `for`, массивы, объекты.
- **IF** — ветвление по условию (boolean, сравнения, строки).
- **NoOp (No Operation)** — нода-заглушка для завершения ветки.

### Сетевые ноды
- **HTTP Request** — GET/POST-запросы к внешним API, парсинг JSON-ответов.

### Redis
- **Redis (Set)** — записать ключ-значение.
- **Redis (Get)** — прочитать значение по ключу.
- **Redis (Delete)** — удалить ключ.
- **Redis (Keys)** — вернуть ключи по шаблону (проверка существования).

### Специальные
- **Credentials** для Redis — Host: `host.docker.internal`, Port: `6379`.

## AI-промпты

При выполнении работы использовались AI-ассистенты (ChatGPT, DeepSeek) для **составления кода в нодах Code** и **консультаций по синтаксису n8n**.

**Примеры ключевых промптов:**

### Промпт 1 — для Code (2.2)

```
Напиши код для ноды Code в n8n (JavaScript), который:
- создаёт массив из 3 книг (title, author, year),
- использует const/let, if/else, цикл for,
- формирует объект result с полями student, topic, booksCount, booksList,
- возвращает массив с объектом { json: { payload: result } }.
```

### Промпт 2 — для HTTP Request (2.5)

```
Помоги настроить ноду HTTP Request в n8n для получения случайного факта о котах с API https://catfact.ninja/fact. В ответе ожидается JSON с полями fact и length. Метод — GET, без авторизации.
```

### Промпт 3 — для Redis credentials

```
В n8n нужно подключиться к Redis, запущенному в отдельном Docker-контейнере. Какой Host указывать в credentials: localhost, 127.0.0.1 или host.docker.internal? Объясни, почему.
```

**Ответ AI:** использовать `host.docker.internal` — это специальное имя Docker, которое указывает на **хост-машину** (Mac) из **внутри контейнера**.

## Скриншоты workflow

Все скриншоты в папке [`screenshots/`](../screenshots/):

- [02-manual-set.png](../screenshots/02-manual-set.png) — workflow 2.1.
- [03-code.png](../screenshots/03-code.png) — workflow 2.2.
- [04-if.png](../screenshots/04-if.png) — workflow 2.3.
- [05-set.png](../screenshots/05-set.png) — workflow 2.4.
- [06-http.png](../screenshots/06-http.png) — workflow 2.5.
- [07-schedule.png](../screenshots/07-schedule.png) — workflow 2.6.
- [08-redis-set-get.png](../screenshots/08-redis-set-get.png) — workflow 3.1 (Redis Set + Get).
- [09-redis-delete.png](../screenshots/09-redis-delete.png) — workflow 3.2 (Redis Delete + Keys).

## Redis — краткое описание

Redis запущен как **отдельный Docker-контейнер**:

```bash
docker run -d --name redis -p 6379:6379 redis:7
```

**Credentials в n8n:**
- **Host:** `host.docker.internal`.
- **Port:** `6379`.
- **Password/User:** пустые.
- **Database:** `0`.

**Два workflow:**

1. **Set + Get** — записать ключ `subotkovskaya` = `Hello from n8n`, прочитать обратно.
2. **Delete + Keys** — удалить ключ, проверить, что его нет.

**Проверка через Redis CLI:**

```bash
docker exec -it redis redis-cli GET subotkovskaya
docker exec -it redis redis-cli EXISTS subotkovskaya
```

Подробнее — в [redis.md](redis.md).

## Выводы

В ходе лабораторной работы №4 я познакомилась с **n8n** — low-code платформой для автоматизации. Установка через Docker оказалась удобной: n8n работает в изолированном контейнере, а данные сохраняются через volume между перезапусками.

Наиболее интересным было изучение **нод для обработки данных** — Set, Code, IF. Они позволяют строить произвольную логику без написания отдельного приложения. Также я освоила **HTTP Request** для интеграции с публичными API.

Отдельно разобралась с **Redis** — установила его как второй Docker-контейнер и настроила credentials в n8n через `host.docker.internal`. Собрала два workflow: для записи/чтения ключа и для удаления/проверки отсутствия. Это дало понимание, как n8n взаимодействует с внешними хранилищами.

## Ссылки

- **Репозиторий:** https://github.com/suvernik-netizen/visual-programming-labs-Subotkovskaya
- **Папка лабораторной:** `lab4/`
- **Workflows:** [`lab4/flows/`](../flows/) — 8 JSON-файлов.
- **Скриншоты:** [`lab4/screenshots/`](../screenshots/) — 8 PNG.
- **Redis-документация:** [redis.md](redis.md).
