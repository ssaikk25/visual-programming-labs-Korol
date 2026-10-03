# Lab2 API — Node-RED GET-эндпоинты

Студент: **Korol**, группа: **ИИ-241**
Поток: `flows/flow-08-endpoints.json`
Базовый адрес: `http://localhost:1880`

> Все эндпоинты создаются нодами `http in` + `http response` (по паре на каждый).
> Ответы `/api/items` формируются в `function`-ноде, статус берётся из `msg.statusCode`.

---

## 1. GET /api/text

Простой текстовый ответ.

- **Метод:** GET
- **Параметры:** нет
- **Ответ:** `200 OK`, `Content-Type: text/plain; charset=utf-8`

Пример:

```bash
curl -i http://localhost:1880/api/text
```

```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8

Привет из Node-RED! Студент: Korol, группа: ИИ-241
```

---

## 2. GET /api/info

JSON с двумя полями.

- **Метод:** GET
- **Параметры:** нет
- **Ответ:** `200 OK`, `Content-Type: application/json`

Пример:

```bash
curl -i http://localhost:1880/api/info
```

```json
{
  "student": "Korol",
  "group": "ИИ-241"
}
```

---

## 3. GET /api/items

Возвращает элемент каталога по `id`. Используются **query params**.

- **Метод:** GET
- **Query-параметры:**

| Параметр | Тип | Обязательный | Описание |
|---|---|---|---|
| `id` | целое число | да | идентификатор элемента (`1..3`) |

**Каталог:**

| id | name | price |
|---|---|---|
| 1 | Ноутбук | 55000 |
| 2 | Мышка | 1500 |
| 3 | Клавиатура | 4500 |

### Успешный запрос — 200

```bash
curl -i "http://localhost:1880/api/items?id=1"
```

```json
{
  "success": true,
  "item": { "id": 1, "name": "Ноутбук", "price": 55000 },
  "student": "Korol",
  "group": "ИИ-241"
}
```

### Ошибка 400 — параметр не передан

```bash
curl -i "http://localhost:1880/api/items"
```

```json
{
  "error": "bad_request",
  "message": "параметр ?id= обязателен",
  "example": "/api/items?id=1",
  "student": "Korol",
  "group": "ИИ-241"
}
```

Статус: `400 Bad Request`.

### Ошибка 400 — id не число

```bash
curl -i "http://localhost:1880/api/items?id=abc"
```

```json
{
  "error": "bad_request",
  "message": "id должен быть целым числом, получено: abc",
  "student": "Korol",
  "group": "ИИ-241"
}
```

Статус: `400 Bad Request`.

### Ошибка 404 — элемент не найден

```bash
curl -i "http://localhost:1880/api/items?id=99"
```

```json
{
  "error": "not_found",
  "message": "элемент с id=99 не найден",
  "student": "Korol",
  "group": "ИИ-241"
}
```

Статус: `404 Not Found`.

---

## Сводная таблица

| Эндпоинт | Статусы | Тело ответа |
|---|---|---|
| `GET /api/text` | 200 | plain text |
| `GET /api/info` | 200 | JSON, 2 поля |
| `GET /api/items?id=...` | 200 / 400 / 404 | JSON |

## Как это устроено в Node-RED

- `http in` (method = GET) — принимает запрос, кладёт объект запроса в `msg.req`
- `change` / `function` — готовит `msg.payload` (и `msg.statusCode` для ошибок)
- `http response` — отправляет ответ; для `/api/items` поле Status оставлено пустым,
  чтобы применялся `msg.statusCode` из function-ноды
