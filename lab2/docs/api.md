# REST API — Node-RED (lab2 flow-08)

Базовый URL: `http://localhost:1880`

## 1. GET /api/text

Возвращает простой текстовый ответ.

**Запрос:** `GET http://localhost:1880/api/text`

**Ответ:** `Это простой текстовый ответ от Node-RED!`

**Content-Type:** `text/plain; charset=utf-8`

---

## 2. GET /api/info

Возвращает JSON с двумя полями.

**Запрос:** `GET http://localhost:1880/api/info`

**Ответ (200):**
```json
{
  "status": "ok",
  "message": "Это информация от Node-RED"
}

### 3.1. Успешный запрос

**Запрос:** `GET http://localhost:1880/api/items?id=42`

**Ответ (200):**
```json
{
  "id": 42,
  "name": "Item 42",
  "description": "Описание элемента #42"
}

### 3.2. Ошибка: параметр не указан (400)

**Запрос:** `GET http://localhost:1880/api/items`

**Ответ (400):**
```json
{
  "error": "Missing parameter",
  "message": "Параметр 'id' обязателен"
}

### 3.3. Ошибка: id вне диапазона (404)

**Запрос:** `GET http://localhost:1880/api/items?id=999`

**Ответ (404):**
{
  "error": "Not found",
  "message": "Элемент с id=999 не найден"
}

### 3.4. Ошибка: id не число (400)

**Запрос:** `GET http://localhost:1880/api/items?id=abc`

**Ответ (400):**
{
  "error": "Invalid parameter",
  "message": "Параметр 'id' должен быть числом"
}
