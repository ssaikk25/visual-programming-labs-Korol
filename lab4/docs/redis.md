# Часть 3. Redis в n8n: создание, чтение и удаление ключа

## 1. Запуск Redis

Redis 7 уже был запущен на стандартном порту `6379` (контейнер `learnhouse-redis`,
образ `redis:7-alpine`), поэтому для лабы использован он — отдельный контейнер не
поднимался, чтобы не занимать порт. В чистом окружении команда из методички:

```powershell
docker run -d --name redis -p 6379:6379 redis:7
```

Проверка доступности:

```powershell
docker exec learnhouse-redis redis-cli ping        # PONG
docker exec learnhouse-redis redis-cli INFO server | Select-String redis_version
```

```
redis_version:7.4.11
```

Доступность порта из контейнера n8n проверена отдельно (`host.docker.internal:6379`) —
TCP-соединение устанавливается.

## 2. Credentials в n8n

`Credentials → Create credential → Redis`, имя **Redis lab4**:

| Поле | Значение |
|---|---|
| Host | `host.docker.internal` |
| Port | `6379` |
| User / Password | пусто (аутентификация не включена) |
| Database Number | `0` |
| SSL | выключен |

После сохранения n8n показал **Connection tested successfully** — значит, контейнер
n8n видит Redis на хосте.

## 3. Workflow 1 — создание и чтение ключа (`flows/flow-09-redis.json`)

```
Manual Trigger → Redis (Set) → Redis (Get)
```

* **Redis: Set korol** — `operation = set`, `key = korol`,
  `value = Korol Artem II-241 | lab4 redis value`, `keyType = string`, `valueIsJSON = false`.
  Флаг `valueIsJSON` выключен, потому что значение — обычная строка, а не JSON.
* **Redis: Get korol** — `operation = get`, `key = korol`, `propertyName = value`,
  `keyType = string`.

Результат выполнения (скриншоты `08-redis-set-get.png`, `09-redis-get-output.png`):

```json
[{ "value": "Korol Artem II-241 | lab4 redis value" }]
```

Проверка снаружи, через `redis-cli`:

```powershell
docker exec learnhouse-redis redis-cli GET korol
# Korol Artem II-241 | lab4 redis value
docker exec learnhouse-redis redis-cli EXISTS korol
# 1
```

## 4. Workflow 2 — удаление и проверка отсутствия (`flows/flow-10-redis.json`)

```
Manual Trigger → Redis (Delete) → Redis (Get) → Code (verify key absent)
```

Важно: в core-ноде **Redis нет операции `Exists`** (доступны `Delete`, `Get`,
`Increment`, `Info`, `Keys`, `List Length`, `Pop`, `Publish`, `Push`, `Set`).
Поэтому отсутствие ключа проверяется через `Get`: если ключа нет, Redis возвращает
`null`, а Code-нода превращает это в `exists: false`.

* **Redis: Delete korol** — `operation = delete`, `key = korol`.
* **Redis: Get korol (exists check)** — `operation = get`, `key = korol`, `propertyName = value`.
* **Code: verify key absent**:

```js
return $input.all().map((item) => {
  const value = item.json.value ?? null;
  return { json: { key: 'korol', exists: value !== null, value } };
});
```

Результат выполнения (скриншоты `10-redis-delete-exists.png`, `11-redis-exists-output.png`):

```json
[{ "key": "korol", "exists": false, "value": null }]
```

Проверка снаружи:

```powershell
docker exec learnhouse-redis redis-cli EXISTS korol
# 0
```

## 5. Скриншоты

| Файл | Что показано |
|---|---|
| `screenshots/08-redis-set-get.png` | workflow Set → Get после успешного запуска |
| `screenshots/09-redis-get-output.png` | OUTPUT ноды Get: прочитанное значение |
| `screenshots/10-redis-delete-exists.png` | workflow Delete → Get → Code после запуска |
| `screenshots/11-redis-exists-output.png` | OUTPUT Code-ноды: `exists = false`, `value = null` |
