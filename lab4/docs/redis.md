# Часть 3. Redis в n8n: создание и удаление ключа

## 1. Запуск Redis в Docker

```powershell
docker run -d --name redis -p 6379:6379 redis:7
```

Проверка:

```powershell
docker exec -it redis redis-cli ping
```

Ожидаемый ответ: `PONG`.

## 2. Credentials в n8n

<!-- TODO: Host: host.docker.internal, Port: 6379, Password: пусто -->

## 3. Workflow 1 — создание и чтение ключа (`flow-09-redis.json`)

<!-- TODO: Manual Trigger → Redis (Set) → Redis (Get) -->

## 4. Workflow 2 — удаление и проверка отсутствия (`flow-10-redis.json`)

<!-- TODO: Manual Trigger → Redis (Delete) → Redis (Exists) -->

## 5. Скриншоты

<!-- TODO -->
