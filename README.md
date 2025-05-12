# 🔐 OTP Protection Service

Java-бэкенд для защиты пользовательских действий с помощью одноразовых паролей (OTP), отправляемых по Email, SMS (через SMPP-эмулятор), Telegram или сохраняемых в файл.

## 📌 Что умеет сервис

- Регистрация и вход с ролями **ADMIN** и **USER**
- Генерация одноразовых кодов (OTP)
  - Отправка по Email (JavaMail)
  - Отправка через SMPP-эмулятор
  - Отправка в Telegram (Bot API)
  - Сохранение в файл
- Проверка OTP по статусам: `ACTIVE`, `USED`, `EXPIRED`
- Панель администратора:
  - Настройка времени жизни и длины OTP
  - Управление пользователями
- Авторизация через токены (JWT)
- Подробное логирование через SLF4J и Logback

## ⚙️ Технологии

- Java 17
- PostgreSQL 17 (через JDBC)
- Maven
- JavaMail
- SMPP (OpenSMPP-core + SMPPsim)
- Telegram Bot API (Apache HttpClient)
- HttpServer (из `com.sun.net.httpserver`)
- SLF4J + Logback

## 🚀 Быстрый старт

### 1. Установите зависимости

- Java 17
- PostgreSQL 17
- Maven

Создайте БД:

```sql
CREATE DATABASE otp_service;
```

### 2. Клонируйте и настройте проект

```bash
git clone https://github.com/airsofter/otp-protection-service.git
cd otp-protection-service
```

Заполните конфигурации в `src/main/resources`:

- `application.properties` — доступ к БД
- `email.properties` — SMTP-настройки
- `sms.properties` — параметры SMPP
- `telegram.properties` — токен и chatId

Пример:

```properties
# application.properties
db.url=jdbc:postgresql://localhost:5432/otp_service
db.user=postgres
db.password=ваш_пароль
```

### 3. Сборка и запуск

```bash
mvn clean package
java -jar target/otp-backend.jar
```

## 📁 Структура проекта

```
otp-protection-service/
├── src/
│   └── main/
│       ├── java/otp/
│       │   ├── api/         # REST API
│       │   ├── config/      # Конфигурация
│       │   ├── dao/         # Работа с БД
│       │   ├── model/       # DTO и сущности
│       │   ├── service/     # Бизнес-логика
│       │   └── util/        # Утилиты
│       └── resources/
│           ├── application.properties
│           ├── email.properties
│           ├── sms.properties
│           ├── telegram.properties
│           └── logback.xml
├── pom.xml
└── README.md
```

## 🔑 Роли

- **ADMIN** — полные права (настройка OTP, работа с пользователями)
- **USER** — генерация и валидация OTP

**Токены** (JWT) передаются в заголовке:

```
Authorization: Bearer <token>
```

## 📡 Примеры API-запросов

### Регистрация

```bash
curl -X POST http://localhost:8080/register   -H "Content-Type: application/json"   -d '{"username":"user1","password":"password123","role":"USER"}'
```

### Авторизация (получение токена)

```bash
curl -X POST http://localhost:8080/login   -H "Content-Type: application/json"   -d '{"username":"user1","password":"password123"}'
```

### Генерация OTP

```bash
curl -X POST http://localhost:8080/otp/generate   -H "Content-Type: application/json"   -H "Authorization: Bearer YOUR_TOKEN"   -d '{"operationId":"op123","channel":"EMAIL"}'
```

### Проверка OTP

```bash
curl -X POST http://localhost:8080/otp/validate   -H "Content-Type: application/json"   -H "Authorization: Bearer YOUR_TOKEN"   -d '{"code":"123456"}'
```

### Админ-функции

Изменение настроек OTP:

```bash
curl -X PATCH http://localhost:8080/admin/config   -H "Content-Type: application/json"   -H "Authorization: Bearer ADMIN_TOKEN"   -d '{"length":6,"ttlSeconds":300}'
```

Список пользователей:

```bash
curl -X GET http://localhost:8080/admin/users   -H "Authorization: Bearer ADMIN_TOKEN"
```

Удаление пользователя:

```bash
curl -X DELETE http://localhost:8080/admin/users/2   -H "Authorization: Bearer ADMIN_TOKEN"
```

## ✅ Проверка работы

Проверьте основные сценарии с помощью Postman или curl:

- Регистрация и логин
- Генерация и отправка OTP
- Проверка кода
- Админ-интерфейс
