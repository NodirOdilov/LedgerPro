<div align="center">

# LedgerPro

**Профессиональная облачная платформа двойной бухгалтерии, финансового учёта и отчётности —
ваш собственный QuickBooks / Xero под полным контролем.**

![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-4.2-092E20?style=for-the-badge&logo=django&logoColor=white)
![DRF](https://img.shields.io/badge/DRF-3.14-A30000?style=for-the-badge&logo=django&logoColor=white)
![React](https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-7-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Celery](https://img.shields.io/badge/Celery-5.3-37814A?style=for-the-badge&logo=celery&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-1.25-009639?style=for-the-badge&logo=nginx&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-FFD700?style=for-the-badge)

</div>

---

## Содержание

1. [О проекте](#1-о-проекте)
2. [Ключевые возможности](#2-ключевые-возможности)
3. [Технологический стек](#3-технологический-стек)
4. [Структура репозитория](#4-структура-репозитория)
5. [Архитектура и как это работает](#5-архитектура-и-как-это-работает)
6. [Доменная модель (крупными блоками)](#6-доменная-модель-крупными-блоками)
7. [Сервисы в Docker Compose](#7-сервисы-в-docker-compose)
8. [Быстрый старт (локально, Docker)](#8-быстрый-старт-локально-docker)
9. [Основные команды Makefile](#9-основные-команды-makefile)
10. [Ручной запуск frontend и backend](#10-ручной-запуск-frontend-и-backend)
11. [Конфигурация и переменные окружения](#11-конфигурация-и-переменные-окружения)
12. [API, очереди и интеграции](#12-api-очереди-и-интеграции)
13. [Мониторинг и эксплуатация](#13-мониторинг-и-эксплуатация)
14. [CI/CD](#14-cicd)
15. [Безопасность и хранение данных](#15-безопасность-и-хранение-данных)
16. [Роли компонентов в продакшене](#16-роли-компонентов-в-продакшене)
17. [Лицензия](#17-лицензия)
18. [Поддержка](#18-поддержка)

---

## 1. О проекте

**LedgerPro** — это **продуктовая облачная SaaS-платформа** для ведения полного бухгалтерского учёта по принципу **двойной записи**: план счетов, журнальные проводки, отчёты P&L / Баланс / Cash Flow, счета-фактуры, расходы, банковские выписки, налоги, мультивалютность и аудит — всё в одном окне. Система рассчитана на конечных пользователей (веб-интерфейс), бухгалтерские команды (workspace + роли) и интеграторов (REST API), с возможностью эксплуатации в Docker — от локальной разработки до production.

### Что это за тип системы

По архитектуре LedgerPro — **многосервисная распределённая платформа** (не монолит «в одном процессе»):

| Аспект         | Описание                                                                       |
| -------------- | ------------------------------------------------------------------------------ |
| **Продукт**    | B2B SaaS-сервис двойной бухгалтерии с отчётами, налогами и аудит-трейлом       |
| **Архитектура**| Многосервисная: Django API, React SPA, Celery workers, Beat scheduler          |
| **Хранилище**  | PostgreSQL (вся бизнес-логика) + Redis (кэш и брокер) + Volumes (статика/медиа)|
| **Деплой**     | Docker Compose локально, Kubernetes / Swarm — в production                     |
| **Аудитория**  | Бухгалтеры, финансовые директора, малый и средний бизнес, аудиторы             |

### Для кого этот проект

* **Финансовые команды** — закрытие периодов, согласование проводок, сверки.
* **Малый и средний бизнес** — счета, расходы, налоги в одной системе.
* **Аудиторы** — read-only роль с полным аудит-трейлом по каждому изменению.
* **Интеграторы / разработчики** — REST API + JWT + OpenAPI-схема.

---

## 2. Ключевые возможности

### Бухгалтерия и учёт

* **Двойная запись** — каждая операция = сбалансированный набор дебет/кредит, целостность гарантирована на уровне БД.
* **План счетов** — иерархическая структура: Активы / Обязательства / Капитал / Доходы / Расходы с неограниченной вложенностью.
* **Журнальные проводки** — создание, ревью, утверждение, проводка и реверс с полным трейлом.
* **Закрытие периодов** — фискальные годы, периоды, заморозка проводок задним числом.
* **Оборотно-сальдовая ведомость** — Trial Balance в любую дату.

### Отчётность

* **Profit & Loss (P&L)** — доходы, расходы, валовая и чистая прибыль с фильтрами по периодам.
* **Balance Sheet** — активы, обязательства, капитал на дату.
* **Cash Flow** — движение денежных средств (операционная, инвестиционная, финансовая).
* **Сравнительные периоды** — месяц-к-месяцу, год-к-году, кастомные диапазоны.
* **Экспорт** — PDF (через WeasyPrint), CSV, Excel.

### Продажи и расчёты

* **Счета-фактуры** — выставление, статусы, частичные оплаты, кредит-ноты.
* **Контрагенты** — клиенты, поставщики, реквизиты, история операций.
* **Платежи** — приём оплат, привязка к счетам, AR/AP-отчёты.
* **Расходы** — категоризация, прикрепление чеков, бюджеты.

### Банкинг и налоги

* **Банковские счета** — импорт выписок (CSV/OFX), ручные операции.
* **Сверка (reconciliation)** — сопоставление банковских строк с проводками.
* **Налоги** — настраиваемые ставки, автоматический расчёт в счетах, налоговые декларации.
* **Мультивалютность** — `django-money`, актуальные курсы, переоценка валютных остатков.

### Платформа и инфраструктура

* **Multi-tenant** — изоляция данных по компаниям, один пользователь = несколько организаций.
* **RBAC** — роли Accountant / Manager / Auditor / Admin с гранулярными правами.
* **Audit Trail** — `django-auditlog` + `django-simple-history` фиксируют кто, когда, что менял.
* **JWT-аутентификация** — `djangorestframework-simplejwt`, refresh/access токены.
* **Celery worker + beat** — фоновые задачи (отчёты, импорт выписок, начисления, e-mail).
* **OpenAPI / Swagger** — автодокументация API через `drf-spectacular`.
* **Docker-first** — один `docker compose up` — и система работает.

---

## 3. Технологический стек

| Слой               | Технологии                                                          |
| ------------------ | ------------------------------------------------------------------- |
| **Язык backend**   | Python 3.11+                                                        |
| **Веб-фреймворк**  | Django 4.2 LTS, Django REST Framework 3.14                          |
| **Аутентификация** | SimpleJWT, django-allauth                                           |
| **Frontend**       | React 18, Redux Toolkit, React Router, Recharts                     |
| **БД**             | PostgreSQL 15                                                       |
| **Кэш / брокер**   | Redis 7 (`django-redis`, Celery broker, sessions cache)             |
| **Очереди**        | Celery 5.3 + django-celery-beat + django-celery-results             |
| **Веб-сервер**     | Nginx 1.25 (reverse proxy, static, media, gzip)                     |
| **WSGI**           | Gunicorn (4 worker × 2 threads)                                     |
| **Документы**      | WeasyPrint (PDF), django-weasyprint                                 |
| **Деньги**         | py-moneyed, django-money, babel (i18n форматы)                      |
| **Аудит**          | django-auditlog, django-simple-history                              |
| **API-схема**      | drf-spectacular (OpenAPI 3), drf-nested-routers                     |
| **Хранилище**      | django-storages + boto3 (опционально S3/MinIO)                      |
| **Тесты**          | pytest, pytest-django, pytest-cov, factory-boy, Faker               |
| **Качество кода**  | black, isort, flake8                                                |
| **Контейнеры**     | Docker, Docker Compose v2                                           |

---

## 4. Структура репозитория

```text
LedgerPro/
├── backend/                          # Django + DRF API
│   ├── apps/                         # Бизнес-домены (по контекстам)
│   │   ├── accounts/                 # Пользователи, компании, фискальные годы, RBAC
│   │   ├── ledger/                   # План счетов, проводки, двойная запись
│   │   ├── invoicing/                # Счета-фактуры, оплаты, кредит-ноты
│   │   ├── expenses/                 # Расходы и категории
│   │   ├── banking/                  # Банковские счета и reconciliation
│   │   ├── reports/                  # P&L, Balance Sheet, Cash Flow
│   │   └── tax/                      # Налоговые ставки и декларации
│   ├── config/                       # Конфигурация проекта
│   │   ├── settings/                 # base / development / production
│   │   ├── celery.py                 # Celery app
│   │   ├── urls.py                   # Корневая маршрутизация
│   │   └── wsgi.py                   # WSGI entrypoint
│   ├── utils/                        # Общие утилиты и хелперы
│   ├── manage.py
│   └── requirements.txt
├── frontend/                         # React 18 SPA
│   ├── public/
│   └── src/
│       ├── api/                      # Axios-клиенты к REST API
│       ├── components/               # Переиспользуемые UI-компоненты
│       ├── pages/                    # Страницы (Dashboard, Ledger, Invoices...)
│       ├── store/                    # Redux Toolkit slices
│       ├── hooks/                    # Кастомные React-хуки
│       ├── styles/                   # Глобальные стили / темы
│       └── utils/                    # Форматтеры дат, чисел, валют
├── nginx/
│   └── nginx.conf                    # Reverse proxy: /api → backend, / → frontend
├── docker-compose.yml                # Сервисы: db, redis, backend, worker, beat, frontend, nginx
└── README.md
```

---

## 5. Архитектура и как это работает

```
                            ┌──────────────────────────┐
                            │   Браузер (пользователь) │
                            └────────────┬─────────────┘
                                         │ HTTPS :80 / :443
                                         ▼
                            ┌──────────────────────────┐
                            │     Nginx Reverse Proxy   │
                            │   gzip · static · media   │
                            └─────┬─────────────┬───────┘
                                  │             │
                  /  /static  /media│             │ /api  /admin  /docs
                                  ▼             ▼
                  ┌────────────────────┐  ┌─────────────────────────┐
                  │   React 18 SPA      │  │   Django 4.2 + DRF API  │
                  │   Redux · Recharts  │  │   Gunicorn (4×2)         │
                  └─────────────────────┘  └────────────┬─────────────┘
                                                        │
                ┌───────────────────────┬───────────────┼───────────────────┬───────────────────────┐
                │                       │               │                   │                       │
                ▼                       ▼               ▼                   ▼                       ▼
      ┌──────────────────┐   ┌──────────────────┐ ┌──────────────┐  ┌──────────────────┐  ┌──────────────────┐
      │  PostgreSQL 15   │   │     Redis 7      │ │ Celery Worker│  │  Celery Beat     │  │  Storage (Vol/S3)│
      │  бизнес-данные   │   │ кэш + брокер     │ │ фоновые job  │  │  расписание      │  │  static + media  │
      └──────────────────┘   └──────────────────┘ └──────────────┘  └──────────────────┘  └──────────────────┘
```

### Поток операции «провести счёт-фактуру»

1. Пользователь в React SPA нажимает «Provести счёт» → `POST /api/invoicing/invoices/{id}/post/`.
2. Nginx маршрутизирует запрос на backend (Gunicorn).
3. Django проверяет JWT, RBAC (роль Accountant/Manager), валидирует период.
4. В транзакции БД создаются `JournalEntry` + `JournalLine` (Дт/Кт), обновляются балансы.
5. `django-auditlog` пишет запись в аудит-трейл.
6. Celery worker асинхронно: рендерит PDF (WeasyPrint), отправляет e-mail клиенту, обновляет AR-отчёт в кэше Redis.
7. Frontend получает ответ, инвалидирует Redux-стор, рисует свежий список.

---

## 6. Доменная модель (крупными блоками)

| Контекст       | Ключевые сущности                                              | Назначение                                       |
| -------------- | -------------------------------------------------------------- | ------------------------------------------------ |
| **accounts**   | `User`, `Company`, `FiscalYear`, `Period`, `Role`, `Membership`| Пользователи, организации, фискальные периоды    |
| **ledger**     | `Account`, `JournalEntry`, `JournalLine`, `Balance`            | План счетов и двойная запись                     |
| **invoicing**  | `Invoice`, `InvoiceLine`, `Payment`, `CreditNote`, `Customer`  | Продажи, выставление счетов, оплаты              |
| **expenses**   | `Expense`, `ExpenseCategory`, `Receipt`, `Vendor`              | Учёт расходов и поставщиков                      |
| **banking**    | `BankAccount`, `BankTransaction`, `Reconciliation`             | Банкинг и сверка с проводками                    |
| **tax**        | `TaxRate`, `TaxCode`, `TaxReturn`                              | Налоги и декларации                              |
| **reports**    | `ReportRun`, `ReportTemplate`                                  | P&L, Balance Sheet, Cash Flow                    |

**Инвариант системы:** для любой `JournalEntry` сумма `JournalLine.debit` строго равна сумме `JournalLine.credit`. Это валидируется на уровне сериализатора и `CheckConstraint` в БД.

---

## 7. Сервисы в Docker Compose

| Сервис          | Образ / сборка         | Порт      | Роль                                                       |
| --------------- | ---------------------- | --------- | ---------------------------------------------------------- |
| `db`            | `postgres:15-alpine`   | 5432      | Основная БД, volume `postgres_data`, healthcheck           |
| `redis`         | `redis:7-alpine`       | 6379      | Кэш + брокер Celery, AOF persistence, пароль из `.env`      |
| `backend`       | `./backend/Dockerfile` | 8000      | Django + Gunicorn, миграции и collectstatic при старте     |
| `celery_worker` | `./backend/Dockerfile` | —         | Фоновые задачи: PDF, e-mail, импорт выписок                |
| `celery_beat`   | `./backend/Dockerfile` | —         | Планировщик (DatabaseScheduler) — переоценка валют, отчёты |
| `frontend`      | `./frontend/Dockerfile`| 3000      | React dev-server (в проде — собранный билд через Nginx)    |
| `nginx`         | `nginx:1.25-alpine`    | 80 / 443  | Reverse proxy, отдача статики и медиа                      |

Все сервисы объединены в одну Docker-сеть, backend и worker ждут `healthy`-статус `db` и `redis` через `depends_on.condition`.

---

## 8. Быстрый старт (локально, Docker)

### Требования

* Docker Engine 24+ и Docker Compose v2
* 4 ГБ свободной RAM
* Свободные порты: **80**, **3000**, **5432**, **6379**, **8000**

### Шаги

```bash
# 1. Клонировать репозиторий
git clone https://github.com/NodirOdilov/LedgerPro.git
cd LedgerPro

# 2. Создать .env (пример ниже в разделе 11)
cp .env.example .env        # либо создайте вручную

# 3. Поднять весь стек
docker compose up --build -d

# 4. Применить миграции (если не выполнились автоматически)
docker compose exec backend python manage.py migrate

# 5. Создать суперпользователя
docker compose exec backend python manage.py createsuperuser

# 6. Засеять стандартный план счетов
docker compose exec backend python manage.py seed_accounts
```

### Точки входа

| Адрес                              | Назначение                          |
| ---------------------------------- | ----------------------------------- |
| `http://localhost`                 | Веб-интерфейс (React SPA)            |
| `http://localhost/api/`            | REST API                            |
| `http://localhost/api/docs/`       | Swagger / OpenAPI документация       |
| `http://localhost/api/admin/`      | Django admin                        |
| `http://localhost:8000/`           | Backend напрямую (минуя Nginx)       |
| `http://localhost:3000/`           | Frontend dev-server (минуя Nginx)    |

---

## 9. Основные команды Makefile

> Если в корне нет `Makefile`, эти команды можно использовать как готовые рецепты `docker compose`.

| Команда            | Что делает                                                    |
| ------------------ | ------------------------------------------------------------- |
| `make up`          | `docker compose up -d --build`                                |
| `make down`        | `docker compose down`                                         |
| `make restart`     | Перезапустить все сервисы                                     |
| `make logs`        | Поток логов всех контейнеров                                  |
| `make migrate`     | Применить миграции                                            |
| `make makemigrations` | Сгенерировать новые миграции                                |
| `make superuser`   | Создать суперпользователя                                     |
| `make shell`       | Django shell внутри backend-контейнера                        |
| `make test`        | Прогон `pytest` с покрытием                                   |
| `make lint`        | `black --check . && isort --check . && flake8`                |
| `make format`      | `black . && isort .`                                          |
| `make seed`        | Загрузить тестовые данные / план счетов                       |
| `make clean`       | Удалить volumes (внимание — снесёт БД)                        |

---

## 10. Ручной запуск frontend и backend

### Backend (без Docker)

```bash
cd backend

python -m venv .venv
# Windows:
.venv\Scripts\activate
# Linux/macOS:
source .venv/bin/activate

pip install -r requirements.txt

# Переменные окружения (минимум):
# DATABASE_URL, REDIS_URL, SECRET_KEY, DJANGO_SETTINGS_MODULE=config.settings.development

python manage.py migrate
python manage.py createsuperuser
python manage.py runserver 0.0.0.0:8000
```

В отдельных терминалах:

```bash
celery -A config worker -l info
celery -A config beat -l info --scheduler django_celery_beat.schedulers:DatabaseScheduler
```

### Frontend (без Docker)

```bash
cd frontend
npm install
npm start   # стартует на http://localhost:3000
```

---

## 11. Конфигурация и переменные окружения

Все секреты и URL вынесены в `.env` в корне проекта. Пример:

```dotenv
# ─── Django ───────────────────────────────────────────────────────────
SECRET_KEY=change-this-in-production
DEBUG=0
ALLOWED_HOSTS=localhost,127.0.0.1
DJANGO_SETTINGS_MODULE=config.settings.production

# ─── PostgreSQL ───────────────────────────────────────────────────────
POSTGRES_DB=ledgerpro
POSTGRES_USER=ledgerpro
POSTGRES_PASSWORD=ledgerpro_secret
POSTGRES_PORT=5432
DATABASE_URL=postgresql://ledgerpro:ledgerpro_secret@db:5432/ledgerpro

# ─── Redis ────────────────────────────────────────────────────────────
REDIS_PASSWORD=redis_secret
REDIS_PORT=6379
REDIS_URL=redis://:redis_secret@redis:6379/0
CELERY_BROKER_URL=redis://:redis_secret@redis:6379/1

# ─── CORS / Frontend ──────────────────────────────────────────────────
CORS_ALLOWED_ORIGINS=http://localhost,http://localhost:3000
REACT_APP_API_URL=http://localhost/api

# ─── Почта (для счетов и уведомлений) ─────────────────────────────────
EMAIL_HOST=smtp.example.com
EMAIL_PORT=587
EMAIL_HOST_USER=noreply@example.com
EMAIL_HOST_PASSWORD=smtp_secret
EMAIL_USE_TLS=1

# ─── Объектное хранилище (опционально, S3/MinIO) ──────────────────────
USE_S3=0
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_STORAGE_BUCKET_NAME=
AWS_S3_ENDPOINT_URL=
```

> В продакшене **обязательно** меняйте `SECRET_KEY`, пароли БД и Redis, ограничивайте `ALLOWED_HOSTS` и `CORS_ALLOWED_ORIGINS`.

---

## 12. API, очереди и интеграции

### Аутентификация (JWT)

| Endpoint               | Метод | Описание                |
| ---------------------- | ----- | ----------------------- |
| `/api/auth/register/`  | POST  | Регистрация             |
| `/api/auth/login/`     | POST  | Получить access+refresh |
| `/api/auth/refresh/`   | POST  | Обновить access-токен   |
| `/api/auth/me/`        | GET   | Текущий пользователь    |

### Главная книга (ledger)

| Endpoint                              | Метод | Описание                       |
| ------------------------------------- | ----- | ------------------------------ |
| `/api/ledger/accounts/`               | GET   | План счетов                    |
| `/api/ledger/accounts/`               | POST  | Создать счёт                   |
| `/api/ledger/journal-entries/`        | GET   | Список проводок                |
| `/api/ledger/journal-entries/`        | POST  | Создать проводку (Дт/Кт сбал.) |
| `/api/ledger/journal-entries/{id}/post/` | POST | Провести (post)               |
| `/api/ledger/trial-balance/`          | GET   | Оборотно-сальдовая             |

### Счета-фактуры и оплаты

| Endpoint                          | Метод | Описание                |
| --------------------------------- | ----- | ----------------------- |
| `/api/invoicing/invoices/`        | GET   | Список счетов           |
| `/api/invoicing/invoices/`        | POST  | Создать счёт            |
| `/api/invoicing/invoices/{id}/pdf/` | GET | Скачать PDF              |
| `/api/invoicing/payments/`        | POST  | Зафиксировать оплату    |

### Отчёты

| Endpoint                          | Метод | Описание               |
| --------------------------------- | ----- | ---------------------- |
| `/api/reports/profit-loss/`       | GET   | Отчёт о прибылях/убытках|
| `/api/reports/balance-sheet/`     | GET   | Бухгалтерский баланс    |
| `/api/reports/cash-flow/`         | GET   | Отчёт о движении ДС     |

> Полная интерактивная схема — `GET /api/docs/` (Swagger UI) и `GET /api/schema/` (OpenAPI 3 YAML).

### Очереди (Celery)

| Тип задачи            | Триггер                         | Описание                                  |
| --------------------- | ------------------------------- | ----------------------------------------- |
| `render_invoice_pdf`  | POST счёта                      | Рендер PDF + сохранение в media           |
| `send_invoice_email`  | POST счёта                      | Отправка клиенту по e-mail                |
| `import_bank_csv`     | Upload выписки                  | Парсинг и создание `BankTransaction`      |
| `revalue_fx_balances` | Beat (раз в день)               | Переоценка валютных остатков по курсам    |
| `nightly_close_check` | Beat (раз в день)               | Проверка целостности проводок за период   |

---

## 13. Мониторинг и эксплуатация

* **Healthchecks** — у `db` и `redis` встроенные healthchecks в `docker-compose.yml`; backend и worker запускаются только когда они `healthy`.
* **Логи** — `docker compose logs -f <service>`. В продакшене рекомендуется агрегатор (Loki/ELK).
* **Метрики** — Django Prometheus exporter (опционально), Celery Flower для очередей: `celery -A config flower`.
* **Sentry** — подключается через `SENTRY_DSN` в `.env` для трекинга исключений.
* **Бэкапы** — `pg_dump` БД + бэкап media-volume по расписанию (cron / Celery beat).
* **Миграции** — выполняются автоматически при старте `backend` (`python manage.py migrate --noinput`).

---

## 14. CI/CD

Рекомендуемый pipeline (GitHub Actions / GitLab CI):

1. **Lint** — `black --check`, `isort --check`, `flake8`, `eslint`.
2. **Tests** — `pytest --cov` для backend, `npm test` для frontend (поднимается postgres + redis service-контейнеры).
3. **Build** — сборка Docker-образов backend и frontend, тег = git SHA.
4. **Push** — `docker push` в registry (GHCR / Docker Hub / ECR).
5. **Deploy** — обновление staging при merge в `develop`, production — при тэге `vX.Y.Z` (с ручным approve).
6. **Smoke-тесты** — после деплоя: `GET /api/health/`, `GET /api/docs/`.

---

## 15. Безопасность и хранение данных

* **JWT с ротацией refresh-токенов** (`SIMPLE_JWT.ROTATE_REFRESH_TOKENS = True`).
* **RBAC** — каждая роль (`Accountant`, `Manager`, `Auditor`, `Admin`) имеет явный набор DRF permission'ов.
* **Двойная запись на уровне БД** — `CheckConstraint`'ы гарантируют `sum(debit) = sum(credit)`.
* **Audit Trail** — `django-auditlog` + `django-simple-history` фиксируют все изменения сущностей.
* **Multi-tenant изоляция** — все запросы фильтруются по `company_id` пользователя через DRF-миксины.
* **CORS / CSRF** — настраиваются через `.env` (`CORS_ALLOWED_ORIGINS`), CSRF включён для admin.
* **HTTPS** — терминируется на Nginx; рекомендуется Let's Encrypt в проде.
* **Пароли** — Argon2 hasher (рекомендация), минимум 12 символов в продакшене.
* **Файлы и медиа** — изолированный volume; для S3/MinIO — pre-signed URLs.
* **Финансовая точность** — все денежные поля — `DecimalField`, никаких `float`.

---

## 16. Роли компонентов в продакшене

| Компонент       | Роль                                                                                            |
| --------------- | ----------------------------------------------------------------------------------------------- |
| **Nginx**       | Единая точка входа, TLS-терминация, gzip, статика, маршрутизация на backend/frontend            |
| **React SPA**   | UI бухгалтера: дашборды, формы, графики (Recharts), скачивание PDF                              |
| **Django API**  | Бизнес-логика, валидация двойной записи, RBAC, отчёты, OpenAPI-схема                            |
| **Gunicorn**    | WSGI-сервер (4 worker × 2 thread), горячий рестарт через `SIGHUP`                               |
| **PostgreSQL**  | Источник правды по всем данным: проводки, счета, балансы                                        |
| **Redis**       | Кэш сериализованных отчётов, sessions, брокер сообщений Celery                                  |
| **Celery worker** | Фоновые задачи: PDF, e-mail, импорт, переоценка                                               |
| **Celery beat** | Планировщик периодических задач (DatabaseScheduler)                                             |
| **Volumes**     | `postgres_data`, `redis_data`, `static_volume`, `media_volume` — персистентное состояние        |

---

## 17. Лицензия

Проект распространяется под лицензией **MIT**. Подробности — в файле [LICENSE](./LICENSE).

---

## 18. Поддержка

* **Issues** — [github.com/NodirOdilov/LedgerPro/issues](https://github.com/NodirOdilov/LedgerPro/issues)
* **Discussions** — обсуждения, фича-реквесты, идеи в разделе Discussions репозитория
* **Автор** — [Nodir Odilov](https://github.com/NodirOdilov)

<div align="center">

---

**LedgerPro** — серьёзная бухгалтерия в одном `docker compose up`.

Сделано с уважением к двойной записи. ⚖️

</div>
