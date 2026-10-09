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

Файлы в `screenshots/`:

| Файл | Что на снимке |
|---|---|
| `01-inject-debug.png` | flow inject → debug + вывод в панели Debug |
| `01-node-version.png` | версия Node.js / Node-RED (логи контейнера) |
| `02-function.png` | function-нода и её результат в debug |
| `03-switch.png` | switch с двумя выходами и два debug |
| `04-change.png` | change: msg до/после (topic, timestamp, payload) |
| `05-template.png` | результат Mustache-шаблона (заполненный JSON) |
| `06-http-request.png` | ответ публичного API в debug |
| `07-mqtt.png` | обе ветки MQTT + входящие сообщения в debug |
| `08-api-text.png` | браузер: `/api/text` |
| `08-api-info.png` | браузер: `/api/info` |
| `08-api-items-200.png` | браузер: `/api/items?id=1` |
| `08-api-items-400.png` | браузер: `/api/items?id=abc` (или без параметра) |
| `08-api-items-404.png` | браузер: `/api/items?id=99` |
| `09-dashboard.png` | дашборд на `/ui`: gauge + график |
| `10-telegram.png` | чат с ботом `tvl_lab2_korol_bot`: /start, /info, echo |
| `11-files-write.png` | запись файла + содержимое на диске |
| `11-files-read.png` | чтение файла после перезапуска Node-RED |
| `12-context.png` | счётчик в flow context и его чтение после Deploy |

---

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
