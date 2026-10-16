# Отчёт по лабораторной работе №4 «n8n»

Студент: **Korol**, группа: **ИИ-241**
Репозиторий: `visual-programming-labs-Korol`, папка работы: `lab4/`

---

## 1. Краткое описание выполненного

<!-- TODO: заполнить после Части 1 и 2 -->

Цель работы — освоить n8n как low-code платформу автоматизации: установка через Docker,
изучение базовых и продвинутых нод, настройка Redis и реализация создания/удаления ключа.

## 2. Часть 1. Установка и запуск n8n

<!-- TODO: образ, версия, команда docker run, volumes, порт, скриншот 01-version.png -->

| Параметр | Значение |
|---|---|
| Образ | `n8nio/n8n` |
| Версия n8n | |
| Порт | `5678` → http://localhost:5678 |
| Volume 1 | `lab4/n8n-data` → `/home/node/.n8n` |
| Volume 2 | `lab4/data` → `/data` |

Команда запуска:

```powershell
docker run -d --name n8n --restart unless-stopped -p 5678:5678 -v D:\visual-programming-labs-Korol\lab4\n8n-data:/home/node/.n8n -v D:\visual-programming-labs-Korol\lab4\data:/data -e GENERIC_TIMEZONE=Europe/Minsk -e TZ=Europe/Minsk n8nio/n8n
```

![Версия n8n](../screenshots/01-version.png)

## 3. Часть 2. Изучение нод и создание workflow

<!-- TODO: описание каждого flow + скриншоты -->

| Workflow | Файл | Ноды |
|---|---|---|
| 2.1 | `flows/flow-01-manual-set.json` | Manual Trigger → Set → Code |
| 2.2 | `flows/flow-02-code.json` | Manual Trigger → Code → NoOp |
| 2.3 | `flows/flow-03-if.json` | Manual Trigger → Code → IF → NoOp / NoOp |
| 2.4 | `flows/flow-04-set.json` | Manual Trigger → Set → Code |
| 2.5 | `flows/flow-05-http.json` | Manual Trigger → HTTP Request → Code |
| 2.6 | `flows/flow-06-schedule.json` | Schedule Trigger → Code |

## 4. Часть 3. Redis: создание и удаление ключа

Подробное описание — в [redis.md](redis.md).

| Workflow | Файл | Ноды |
|---|---|---|
| Создание и чтение | `flows/flow-09-redis.json` | Manual Trigger → Redis (Set) → Redis (Get) |
| Удаление и проверка | `flows/flow-10-redis.json` | Manual Trigger → Redis (Delete) → Redis (Exists) |

## 5. Использованные AI-промпты

<!-- TODO: несколько ключевых промптов -->

## 6. Освоенные ноды

<!-- TODO -->

## 7. Скриншоты workflow

<!-- TODO: вставить изображения из ../screenshots/ -->

## 8. Выводы

<!-- TODO: своими словами -->
