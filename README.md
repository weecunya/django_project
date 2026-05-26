Django Authentication and RBAC System

Проект представляет собой backend-приложение с собственной системой аутентификации и ролевой моделью доступа (RBAC). Реализована на Django + Django REST Framework.

Технологии

· Python 3.12
· Django 6.0
· Django REST Framework
· JWT (PyJWT) для аутентификации
· SQLite (локально) / PostgreSQL (в Docker)
· Docker и Docker Compose
· pytest для тестирования
· Gunicorn для продакшена

Особенности проекта

· Собственная система аутентификации на JWT-токенах (не используется стандартная django.contrib.auth в чистом виде)
· Собственная система авторизации: роли, ресурсы, права (RBAC)
· Мягкое удаление пользователей (is_active=False)
· Административные эндпоинты для управления пользователями и ролями
· Тесты с покрытием ключевых модулей
· Докеризация для лёгкого развёртывания

Структура базы данных

Таблица users

· id (PK)
· email (unique)
· password (хешированный)
· first_name, last_name
· is_active (флаг мягкого удаления)
· created_at, updated_at

Таблица roles

· id (PK)
· name (admin, user)

Таблица resources

· id (PK)
· name (profile, project_list, user_permissions)

Таблица permissions

· id (PK)
· role_id (FK -> roles)
· resource_id (FK -> resources)
· can_read (boolean)
· can_write (boolean)
· can_delete (boolean)

Таблица user_roles

· user_id (FK -> users)
· role_id (FK -> roles)

Запуск проекта

Локальный запуск без Docker

1. Создать виртуальное окружение и активировать его
2. Установить зависимости: pip install -r requirements.txt
3. Создать .env файл с переменными окружения (см. .env.example)
4. Выполнить миграции: python manage.py migrate
5. Загрузить тестовые данные: python load_data.py
6. Запустить сервер: python manage.py runserver

Запуск через Docker

1. Собрать образ: docker build -t django-auth-app .
2. Запустить контейнер: docker run -p 8000:8000 django-auth-app

Запуск через Docker Compose

1. docker-compose up --build
2. Приложение будет доступно по адресу http://localhost:8000

API Эндпоинты

Публичные эндпоинты

Метод Эндпоинт Описание
POST /api/register/ Регистрация пользователя
POST /api/login/ Вход, получение JWT-токена

Защищённые эндпоинты (требуют JWT-токен в заголовке Authorization: Bearer <token>)

Метод Эндпоинт Описание Права
GET /api/profile/ Получение своего профиля авторизация
PUT /api/profile/update/ Обновление профиля авторизация
DELETE /api/profile/delete/ Мягкое удаление аккаунта авторизация
GET /api/projects/ Список проектов read на project_list
POST /api/projects/create/ Создание проекта write на project_list
DELETE /api/projects/<id>/delete/ Удаление проекта delete на project_list

Административные эндпоинты (требуют роль admin)

Метод Эндпоинт Описание Права
GET /api/admin/users/ Список всех пользователей read на user_permissions
PUT /api/admin/users/<id>/role/ Смена роли пользователя write на user_permissions

Тестирование

Запуск всех тестов:
pytest tests/ -v

Просмотр покрытия:
pytest --cov=auth_app tests/

Тестовые учётные записи

После выполнения python load_data.py доступны:

Роль Email Пароль
Администратор admin@example.com admin123
Обычный пользователь user@example.com 123

Переменные окружения (.env)

Для работы проекта требуется создать файл .env в корне со следующими переменными:
SECRET_KEY=your-django-secret-key
DEBUG=True
ALLOWED_HOSTS=127.0.0.1,localhost
JWT_SECRET_KEY=your-jwt-secret-key

Структура проекта
django_test_project/
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── manage.py
├── .env.example
├── pytest.ini
├── myproject/
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── auth_app/
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   ├── permissions.py
│   ├── services.py
│   ├── decorators.py
│   ├── authentication.py
│   └── middleware.py
├── tests/
│   ├── test_auth.py
│   ├── test_users.py
│   ├── test_projects.py
│   └── conftest.py
└── load_data.py

Автор

Виктория Заверская

GitHub: weecunya

Лицензия

MIT
