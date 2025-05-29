[![Main Kittygram workflow](https://github.com/ilitvikh/kittygram_final/actions/workflows/main.yml/badge.svg)](https://github.com/ilitvikh/kittygram_final/actions/workflows/main.yml)

# Kittygram

## Блог для владельцев котов

### Возможности:
- Информация о котах с изображениями

### Стек технологий:
- Python
- Django
- Django REST Framework
- JavaScript
- Node.js

---

## Запуск проекта

Инструкция по автоматическому разворачиванию и деплою проекта с использованием GitHub Actions, Docker и Docker Compose.

---

## Автоматический деплой

### Когда срабатывает

CI/CD workflow срабатывает автоматически при push в репозиторий.  
Деплой на сервер происходит только при пуше в ветку `main`.

---

## Состав workflow

### 1. Тесты (Backend)

- Проверка кода с помощью `flake8`
- Тесты выполняются на Python 3.9 и 3.10

### 2. Сборка и публикация Docker-образов

- Backend → `kittygram_backend:latest`
- Frontend → `kittygram_frontend:latest`
- Gateway (Nginx) → `kittygram_gateway:latest`
- Публикация в DockerHub (используются секреты `DOCKER_USERNAME` и `DOCKER_PASSWORD`)

### 3. Деплой на сервер

- Копирование `docker-compose.production.yml` на сервер
- Pull образов из DockerHub
- Перезапуск всех контейнеров
- Выполнение:
  - Миграций: `python manage.py migrate`
  - Сборки статики: `collectstatic`
  - Копирование статики в `/backend_static/`

### 4. Telegram-уведомление

После успешного деплоя отправляется сообщение в Telegram-чат.

---

## Необходимые secrets в GitHub

| Название         | Описание                            |
|------------------|-----------------------------------|
| DOCKER_USERNAME  | Логин DockerHub                   |
| DOCKER_PASSWORD  | Пароль DockerHub                  |
| HOST             | IP-адрес сервера                  |
| USER             | SSH-пользователь                  |
| SSH_KEY          | Приватный SSH-ключ                |
| SSH_PASSPHRASE   | Пароль SSH-ключа (если требуется) |
| TELEGRAM_TO      | ID Telegram-чата                  |
| TELEGRAM_TOKEN   | Токен Telegram-бота               |

---

## Структура проекта и Docker-образов

- `./backend/` — Backend-приложение на Django  
- `./frontend/` — React frontend  
- `./nginx/` — Nginx-прокси (gateway)  
- `docker-compose.production.yml` — конфигурация Docker Compose для продакшн-сборки  

---

## Пример ручного деплоя через SSH

Если необходимо, можно вручную выполнить деплой, подключившись к серверу:

```bash
ssh user@your_host
cd Kittygram
docker compose -f docker-compose.production.yml pull
docker compose -f docker-compose.production.yml down
docker compose -f docker-compose.production.yml up -d
docker compose -f docker-compose.production.yml exec backend python manage.py migrate
docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic --noinput
docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/
```

---

## Настройка файла `.env`

Для корректной работы проекта создайте в корне backend-приложения файл `.env` со следующими переменными окружения (замените значения на свои):

```env
POSTGRES_DB=your_database_name
POSTGRES_USER=your_database_user
POSTGRES_PASSWORD=your_database_password
DB_NAME=your_database_name
DB_HOST=db
DB_PORT=5432
SECRET_KEY=your_django_secret_key
DEBUG=True
ALLOWED_HOSTS=your_domain_or_ip,localhost,127.0.0.1
```

---

### Автор:  
ilitvikh [GitHub](https://github.com/ilitvikh)
