# Отчёт по лабораторной работе №2 — Node-RED

## Краткое описание выполненного

В ходе лабораторной работы №2 был освоен Node-RED — low-code инструмент для визуального программирования. Node-RED запущен в Docker-контейнере с сохранением данных через volume `node_red_data`. Создано **12 потоков (flows)**, демонстрирующих базовые и расширенные возможности Node-RED: от простого inject → debug до MQTT, REST API, Dashboard и Telegram-бота.

Все потоки сохранены в формате JSON в папке `lab2/flows/`, скриншоты — в `lab2/screenshots/`.

## Способ установки и версии

**Способ установки:** Docker.

**Команда запуска:**

```bash
docker run -d -p 1880:1880 -v node_red_data:/data --name mynodered nodered/node-red
```

**Версии (из логов контейнера):**
- **Node-RED:** `v5.0.7`
- **Node.js:** `v24.20.0`

**Установленные дополнительные пакеты:**
- `node-red-dashboard` (версия 3.6.6) — для dashboard с gauge и графиком
- `node-red-contrib-telegrambot` — для Telegram-бота

## Освоенные ноды

### Базовые ноды
- **inject** — источник сообщений (ручной запуск, интервал, по расписанию).
- **debug** — вывод сообщений в отладочную панель.
- **function** — JavaScript-код для обработки сообщений.
- **switch** — ветвление по условию.
- **change** — изменение свойств сообщения (msg.payload, msg.topic, msg.timestamp).
- **template** — шаблонизация через Mustache.

### Сетевые ноды
- **http in** — приём HTTP-запросов (REST API).
- **http response** — отправка HTTP-ответов.
- **http request** — исходящие HTTP-запросы к внешним API.
- **mqtt in / mqtt out** — работа с MQTT-брокером (публичный broker.hivemq.com).

### Dashboard
- **gauge** — стрелочный индикатор.
- **chart** — график истории значений.

### Telegram
- **telegram receiver** — приём сообщений от Telegram-бота.
- **telegram sender** — отправка ответов.

### Работа с файлами
- **write file** — запись в файл.
- **read file** — чтение файла.

### Работа с контекстом
- **`flow.get()` / `flow.set()`** — сохранение значений между сообщениями (счётчик).

## Скриншоты всех flow

| # | Поток | Скриншот |
|---|---|---|
| 2.1 | Inject → Debug | [01-inject-debug.png](../screenshots/01-inject-debug.png) |
| 2.2 | Function | [02-function.png](../screenshots/02-function.png) |
| 2.3 | Switch | [03-switch.png](../screenshots/03-switch.png) |
| 2.4 | Change | [04-change.png](../screenshots/04-change.png) |
| 2.5 | Template | [05-template.png](../screenshots/05-template.png) |
| 2.6 | HTTP Request | [06-http-request.png](../screenshots/06-http-request.png) |
| 2.7 | MQTT | [07-mqtt.png](../screenshots/07-mqtt.png) |
| 2.8 | GET-эндпоинты | [08-endpoints.png](../screenshots/08-endpoints.png) |
| 2.9 | Dashboard | [09-dashboard.png](../screenshots/09-dashboard.png), [09-dashboard-flow.png](../screenshots/09-dashboard-flow.png) |
| 2.10 | Telegram-бот | [10-telegram.png](../screenshots/10-telegram.png) |
| 2.11 | Файлы | [11-files.png](../screenshots/11-files.png) |
| 2.12 | Контекст | [12-context.png](../screenshots/12-context.png) |

## Использованные AI-промпты

При выполнении работы использовались AI-ассистенты (ChatGPT, DeepSeek) для генерации кода и консультаций. **Ключевые промпты:**

1. **Для Function node (2.2):**
   > «Сгенерируй код для ноды function в Node-RED. Требования: использовать let/const, if/else, цикл for, массив, объект. Функция возвращает объект с полем payload. Код должен быть понятным для студента.»

2. **Для шаблона Template (2.5):**
   > «Сгенерируй Mustache-шаблон для ноды template в Node-RED. На вход приходит объект с 3 полями: name, age, city. Шаблон формирует JSON с подстановкой значений.»

3. **Для function Telegram-бота (2.10):**
   > «Помоги написать код для function в Node-RED, обрабатывающий команды Telegram-бота: /start — приветствие, /echo <текст> — повтор текста, любое другое сообщение — эхо-ответ.»

## Документация API

Документация REST API-эндпоинтов находится в файле [api.md](api.md).

## Выводы

В ходе лабораторной работы я научилась работать с Node-RED — инструментом для визуального программирования. Потоки собираются из готовых блоков, которые соединяются стрелками, — писать код с нуля не нужно.

Node-RED я установила через Docker. Все потоки, настройки и файлы сохраняются в отдельном хранилище (volume) и не теряются при перезапуске.

В работе были созданы потоки с MQTT (обмен сообщениями через публичный брокер), REST API-эндпоинты с проверкой параметров и Telegram-бот, который отвечает на команды.

Также я разобралась, как сохранять значения между сообщениями с помощью flow context.

## Ссылки

- **Репозиторий:** https://github.com/suvernik-netizen/visual-programming-labs-Subotkovskaya
- **Папка lab2:** `lab2/`
- **Flows:** `lab2/flows/` — 12 JSON-файлов
- **Скриншоты:** `lab2/screenshots/` — 13 PNG-файлов
- **Документация API:** [api.md](api.md)
