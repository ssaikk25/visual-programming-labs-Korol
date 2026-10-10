# Отчёт по лабораторной работе №2 «Node-RED»

Студент: **Korol**, группа: **ИИ-241**
Репозиторий: `visual-programming-labs-Korol`, папка работы: `lab2/`

---

## 1. Краткое описание выполненного

Работа выполнена в **Docker**: контейнер `node-red` запущен из образа `nodered/node-red:latest`
с volume `lab2/node-red-data → /data` и пробросом порта `1880`, редактор доступен на
http://localhost:1880.

Собраны и задеплоены все потоки из Части 2 (каждый на своей вкладке, каждый сохранён
отдельным файлом в `flows/`):

| № | Поток | Файл | Ноды |
|---|---|---|---|
| 2.1 | inject → debug | `flow-01-inject-debug.json` | inject, debug |
| 2.2 | разбор числа в функции | `flow-02-function.json` | inject, function, debug |
| 2.3 | ветвление по статусу | `flow-03-switch.json` | inject, function, switch, debug ×2 |
| 2.4 | изменение полей сообщения | `flow-04-change.json` | inject, change, debug |
| 2.5 | Mustache-шаблон → JSON | `flow-05-template.json` | inject, template, debug |
| 2.6 | запрос к публичному API | `flow-06-http-request.json` | inject, http request, debug |
| 2.7 | MQTT pub/sub | `flow-07-mqtt.json` | inject, function, mqtt out, mqtt in, debug |
| 2.8 | три GET-эндпоинта | `flow-08-endpoints.json` | http in ×3, change, function ×2, http response ×3 |
| 2.9 | дашборд с датчиком | `flow-09-dashboard.json` | inject, function, ui_gauge, ui_chart |
| 2.10 | Telegram-бот | `flow-10-telegram.json` | telegram bot, telegram command ×2, telegram receiver, function ×3, telegram sender, debug ×2 |
| 2.11 | Чтение и запись файла | `flow-11-files.json` | inject ×2, function, file out, file in, debug ×2 |
| 2.12 | Работа с контекстом | `flow-12-context.json` | inject ×3, function ×3, debug ×3 |

Каждый поток проверен вживую: debug-вывод, ответы браузера по трём URL, публикация и
приём сообщений через публичный брокер `broker.hivemq.com:1883`, живой дашборд на
http://localhost:1880/ui.

Для уникальности во всех потоках используются фамилия и группа: `msg.topic` вида
`lab2/Korol-ИИ-241/...`, MQTT-топик `student/Korol-ИИ-241/lab2/sensor`, фамилия и
группа в именах нод, payload и названиях вкладок.

Документация API (эндпоинты, параметры, curl-примеры, ответы 200/400/404) — в `docs/api.md`.

---

## 2. Какие AI-промпты использовались (ключевые примеры)

Промпты приведены по смыслу, дословно не все — ниже самые значимые:

1. «Разбери задание из PDF лабораторной работы по Node-RED и перечисли все пункты,
   которые нужно выполнить» — для анализа задания и составления плана работ.

2. «Сгенерируй код для function-нода на JavaScript: на входе число, нужно раскладdigit
   по разрядам в цикле, посчитать сумму, вернуть объект с полями — с использованием
   let/const, if/else, for, массива и объекта» — код function-нода в 2.2/2.3.

3. «Напиши Mustache-шаблон, который из объекта `{student, group, score}` собирает
   JSON» — шаблон для 2.5. **Важный момент:** AI изначально предложил обращения вида
   `{{student}}`, но контекстом шаблона в Node-RED является весь `msg`, поэтому
   правильный вариант — `{{payload.student}}`. Это найдено при сверке с исходниками
   ноды template и исправлено.

4. «Сделай function-ноду для GET /api/items: принимает query-параметр id, возвращает
   400 если параметр не передан или id не число, 404 если элемент не найден, иначе 200
   с данными» — логика третьего эндпоинта в 2.8.

5. «Сгенерируй JSON для импорта ноды ui_gauge и ui_chart в Node-RED с вкладкой и
   группой dashboard» — каркас 2.9.

6. «Почему template-нода возвращает пустые поля?» — диагностика бага 2.5
   (контекст Mustache = весь msg, а не msg.payload).

7. «Проверь JSON flow-файлов: число выходов function-нода против числа проводов» —
   так найден баг в 2.9 (1 выход, 2 провода), исправлен на один выход с двумя
   получателями.

ИИ использовался на каждом из шагов; итоговые результаты проверены и защищаются студентом.

---

## 3. Какие ноды освоены

**Базовые:** inject, debug, function, switch, change, template
**Сетевые:** http request, http in, http response, mqtt in, mqtt out (+ config mqtt-broker)
**Dashboard:** ui_tab, ui_group, ui_gauge, ui_chart (+ ui_base)
**Telegram:** telegram bot (config), telegram command, telegram receiver, telegram sender
**Файлы:** file out (`file`), file in
**Контекст:** flow context, global context (flow.get/set, global.get/set)

**Дополнительно освоено:**
- импорт/экспорт flow-файлов (☰ → Import / Export);
- деплой, статусы нод, панель Debug (payload / complete msg object);
- контексты сообщения: `msg.payload`, `msg.topic`, `msg.statusCode`, `msg.req.query`;
- разница между `http in` + `http response` (HTTP-сервер внутри Node-RED) и
  `http request` (HTTP-клиент);
- MQTT-топики, QoS 0, публичный брокер, статусы connected/disconnected;
- работа dashboard-виджетов на `/ui`, назначение вкладок и групп;
- Mustache-шаблоны и их контекст (весь `msg`).

---

## 4. Способ установки и версии

- **Способ установки:** Docker (образ `nodered/node-red:latest`)
- **Запуск:** `docker run -d --name node-red -p 1880:1880
  -v "D:\visual-programming-labs-Korol\lab2\node-red-data:/data" nodered/node-red:latest`
- **Volume:** `lab2/node-red-data` → `/data` в контейнере (настройки и ноды переживают перезапуск)
- **Порт:** 1880
- **Node-RED:** v5.0.7 (образ `nodered/node-red:latest`)
- **Node.js:** v24.20.0 (внутри контейнера)
- **Версия Docker-клиента:** 29.8.0
- **Способ проверки:** `docker ps` показывает контейнер `node-red`;
  версии — из `docker logs node-red` и меню ☰ → About

_Команды для получения версий:_
```powershell
docker --version
docker logs node-red | Select-String "Node.js version|Node-RED version"
docker exec node-red node -v
```

**Установленные дополнительные ноды:** `node-red-dashboard` 3.6.6 и `node-red-contrib-telegrambot` 19.0.3 (через ☰ → Manage palette).

---

## 5. Скриншоты

Все снимки лежат в `screenshots/`. Ниже — встроенные изображения по каждому пункту.

### 2.1 Inject → Debug
![2.1 inject → debug](../screenshots/01-inject-debug.png)

### Версии Node-RED / Node.js
![версии](../screenshots/01-node-version.png)

### 2.2 Function node
![2.2 function](../screenshots/02-function.png)

### 2.3 Switch node
![2.3 switch](../screenshots/03-switch.png)

### 2.4 Change / Set node
![2.4 change](../screenshots/04-change.png)

### 2.5 Template node
![2.5 template](../screenshots/05-template.png)

### 2.6 HTTP Request node
![2.6 http request](../screenshots/06-http-request.png)

### 2.7 MQTT
![2.7 mqtt](../screenshots/07-mqtt.png)

### 2.8 GET-эндпоинты

`/api/text`:

![2.8 api text](../screenshots/08-api-text.png)

`/api/info`:

![2.8 api info](../screenshots/08-api-info.png)

`/api/items?id=1` (200):

![2.8 items 200](../screenshots/08-api-items-200.png)

`/api/items?id=abc` (400):

![2.8 items 400](../screenshots/08-api-items-400.png)

`/api/items?id=99` (404):

![2.8 items 404](../screenshots/08-api-items-404.png)

### 2.9 Dashboard
![2.9 dashboard](../screenshots/09-dashboard.png)

### 2.10 Telegram-бот
![2.10 telegram](../screenshots/10-telegram.png)

### 2.11 Файлы (запись)
![2.11 files write](../screenshots/11-files-write.png)

### 2.12 Контекст
![2.12 context](../screenshots/12-context.png)

### Ачивка 16 — subflow (use 1)
![ачивка 16 use 1](../screenshots/16-subflow-use1.png)

### Ачивка 16 — subflow (use 2)
![ачивка 16 use 2](../screenshots/16-subflow-use2.png)

## 6. Выводы (своими словами)

В ходе работы освоил Node-RED как low-code инструмент: наглядно, что поток данных
описывается графиком из нод, а не кодом. Понял разницу между узлами разных ролей:
inject — источник сообщения, function — место для произвольной JavaScript-логики,
switch — ветвление по условию, change — изменение полей без кода, template —
генерация текста/JSON по шаблону.

Отдельно разобрался с двумя «сетевыми» направлениями. `http request` — это клиент,
который ходит наружу в публичные API, а `http in` + `http response` — мини-HTTP-сервер
внутри самого Node-RED: с его помощью удалось развернуть три GET-эндпоинта с
валидацией и кодами 200/400/404, документацию к ним оформил в `api.md`.

MQTT показалась самой живой частью: настроил публикацию и подписку через публичный
брокер HiveMQ, увидел, что сообщение уходит наружу и возвращается обратно в ту же
ветку потока — это хорошая основа для работы с реальными датчиками и IoT-сценариями.

Dashboard превратил числовой поток в нормальный UI: gauge мгновенно показывает
текущее значение, chart — историю, и всё обновляется автоматически по таймеру.

Полезным оказался и опыт отладки: пришлось разобраться, почему Mustache-шаблон
возвращает пустые поля (контекст шаблона — это весь `msg`, а не `msg.payload`), и
найти ошибку с числом выходов у function-нода. Вывод: даже в low-code инструменте
нужно читать документацию и проверять логи, а не полагаться на интуицию.

---

## 7. План защиты

1. Показать контейнер: `docker ps`, версии из `docker logs`
2. Пройтись по вкладкам 2.1 → 2.9, объяснить каждую ноду
3. Показать историю коммитов: `git log --oneline`
4. Объяснить: устройство MQTT (топик, брокер, pub/sub), работу GET-эндпоинтов
   (http in → function → http response, `msg.statusCode`), dashboard (injected →
   gauge/chart), контекст сообщения и контексты flow/global
5. Ответить на вопросы по `api.md` и устройству function-нод

---

## 8. Ачивка 16 — Subflow для работы с файлами

### Что такое subflow

Subflow — это группа нод, свёрнутая в одну **переиспользуемую** ноду. Внутри subflow
лежит обычный поток (function, file, http и т.д.), а снаружи он выглядит как одна
нода в палитре: её можно ставить в любые flow сколько угодно раз. Если нужно
поменять логику — правишь один раз внутри subflow, и это применяется сразу во всех
местах использования.

### Зачем нужен

- **Не дублировать код** — одинаковую логику описываешь один раз
- **Упрощать большие flow** — часть схемы прячется в одну ноду, читается легче
- **Единая точка правки** — исправление в одном месте меняет все копии
- **Передача параметров** — через `msg` или переменные окружения subflow (env)

### Что сделано

Созданы два subflow (файл `flows/flow-16-subflow.json`), каждый свёрнут в одну ноду:

| Subflow | Внутри | Назначение |
|---|---|---|
| **Сохранить JSON в файл (Korol ИИ-241)** | function → file out | берёт объект из `msg.payload` и путь из `msg.filename`, перезаписывает JSON-файл |
| **Прочитать JSON из файла (Korol ИИ-241)** | file in → function (`JSON.parse`) | читает файл по `msg.filename`, возвращает распарсенный объект |

Параметры (путь и имя файла) передаются **через msg** (`msg.filename`), а не
хардкодятся внутри — это выполнение усложнения из задания.

Демонстрация переиспользования — те же subflow поставлены в **два разных flow**:

1. Вкладка **«Lab2 А16 Subflow use 1»** — файл `/data/korol-a16-users.json`
2. Вкладка **«Lab2 А16 Subflow use 2»** — файл `/data/korol-a16-sensors.json`

В каждой: inject задаёт `msg.filename` и объект → subflow записи сохраняет файл,
второй inject → subflow чтения возвращает распарсенный объект в debug. Скриншоты —
`screenshots/16-subflow-use1.png` и `16-subflow-use2.png`.

### В чём был подвох

Первый вариант subflow записи использовал **дозапись** (`append`), из-за чего после
нескольких запусков в файле оказывалось несколько JSON-строк, а `JSON.parse` в
subflow чтения ломался с ошибкой «Unexpected non-whitespace character after JSON».
Исправлено: subflow записи теперь **перезаписывает** файл (хранит ровно один объект),
и чтение всегда успешно парсит содержимое. Это хороший пример того, что у subflow
важна не только внутренняя логика, но и согласованность формата данных между
входом и выходом.
