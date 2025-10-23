# 🐟 RandAqua - Прозрачный Генератор Случайных Чисел

> **Революционная система генерации случайных чисел на основе наблюдения за морской жизнью**

[![Next.js](https://img.shields.io/badge/Next.js-15.5.6-black)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19.1.0-blue)](https://reactjs.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4.0-38B2AC)](https://tailwindcss.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 🎯 О проекте

**RandAqua** — это инновационный генератор случайных чисел, который решает проблему доверия через технологическую прозрачность. Наша система использует уникальный источник энтропии — наблюдение за движением морских обитателей в аквариумах — для создания истинно случайных чисел.

### 🌊 Ключевые особенности

- **🐟 Уникальный источник энтропии**: Камеры видеонаблюдения за рыбками создают непредсказуемые паттерны движения
- **🔍 Полная прозрачность**: Визуализация всего процесса от сбора энтропии до генерации числа
- **📊 Статистические тесты**: Автоматическая проверка случайности с помощью NIST STS и других алгоритмов
- **🎲 Интерактивная демонстрация**: Пошаговое объяснение работы алгоритма
- **📁 Анализ внешних данных**: Проверка случайности любых текстовых последовательностей
- **💾 Цифровые слепки**: Сохранение полной информации о процессе генерации

## 🏗️ Архитектура проекта

Проект состоит из двух основных компонентов:

### 🎨 Frontend (`/generator`)
- **Next.js 15.5.6** - React фреймворк с SSR
- **React 19.1.0** - Библиотека пользовательского интерфейса
- **Tailwind CSS 4.0** - Utility-first CSS фреймворк
- **JavaScript ES6+** - Современный JavaScript

## ⚙️ Backend

- Язык/Стек: FastAPI (Python), .NET 8 (C#), Postgres, MSSQL, Redis, RabbitMQ
- Запуск: один файл `docker-compose.yml` поднимает весь бэкенд-стек

### Быстрый старт (Docker)
```bash
# из корня репозитория
docker compose build
docker compose up -d
```

- API (main-service): http://localhost:8000/docs
- Authorization (.NET): http://localhost:7177/swagger
- Generation (.NET): http://localhost:7134/swagger
- Analysis (.NET): http://localhost:7182/swagger
- MailHog (почта dev): http://localhost:8025

### Сервисы
- main-service (FastAPI, порт 8000 наружу, 8001 внутри): оркеструет вызовы, анализ видео (YOLO/OpenCV), прокси к C# сервисам
- authorization (.NET, 7177): регистрация, логин, подтверждение e‑mail
- generation (.NET, 7134): генерация чисел/слепков; пишет файлы на file-server
- analysis (.NET, 7182): статистические тесты случайности (NIST и пр.)
- file-server (внутренний 8002): хранилище файлов/истории
- Инфра: Postgres (main/file), MSSQL (authorization), Redis (rate-limit/cache), RabbitMQ, MailHog (SMTP dev)

### Эндпоинты (основные)
main-service (http://localhost:8000):
- POST `/auth/register` — регистрация (прокси в authorization)
- POST `/auth/login` — вход (прокси)
- POST `/auth/verify-code` — подтверждение кода (прокси)
- POST `/video/generate-batch-snapshot` — анализ 10с видео и вызов генерации
- POST `/video/generate-binary-seeds` — 6 сидов из случайных сегментов видео
- Swagger: `/docs`

authorization (http://localhost:7177):
- POST `/api/auth/register`, `/api/auth/login`, `/api/Auth/confirm-email`, `/api/Auth/get-user-info`

generation (http://localhost:7134):
- POST `/api/RandomGenerator/generate/batch/snapshot`
- POST `/api/RandomGenerator/generate/binary-seeds`

analysis (http://localhost:7182):
- Swagger с тестами случайности

### Видео и модели
- Входное видео: `mainservise/video_storage/current_video.mp4` → монтируется как `/app/video_storage/current_video.mp4`
- YOLO-модель: `mainservise/neiro/best.pt` → копируется в образ (`/app/neiro/best.pt`)

### Почта
- По умолчанию письма идут в MailHog (dev). UI: http://localhost:8025
- Для боевого SMTP задайте в `docker-compose.yml` для `authorization`:
  - `EmailSettings__SmtpHost`, `EmailSettings__SmtpPort`, `EmailSettings__SmtpUsername`, `EmailSettings__SmtpPassword`, `EmailSettings__FromEmail`, `EmailSettings__EnableSsl=true`
  - затем: `docker compose up -d authorization`

### Полезные команды
```bash
# логи сервиса
docker compose logs -f main-service
# пересобрать и перезапустить один сервис
docker compose build authorization && docker compose up -d authorization
# остановить всё
docker compose down
```


## 🚀 Быстрый старт

### Frontend
```bash
cd generator
npm install
npm run dev
```
Откройте [http://localhost:3000](http://localhost:3000)

### Backend
```bash
cd backend
# Инструкции по запуску backend команды
```

## 🎮 Сценарии использования

### 1. 🎲 Лотерейный тираж
- Генерация выигрышных комбинаций
- Визуализация процесса создания случайности
- Создание цифрового слепка для аудита

### 2. 🔍 Аудит внешнего генератора
- Загрузка и анализ сторонних последовательностей
- Визуальный отчет о качестве случайности
- Сравнение с эталонными генераторами

### 3. 🎓 Демонстрация работы
- Интерактивное объяснение принципов работы
- Пошаговая визуализация алгоритма
- Образовательные материалы о случайности

## 🧪 Тестирование случайности

Наша система проходит строгие статистические тесты:

- **NIST STS** - Национальный институт стандартов и технологий
- **Dieharder** - Расширенный набор тестов случайности
- **TestU01** - Универсальный тестовый пакет

### Генерация тестовых данных

Система автоматически генерирует файлы с ≥ 1,000,000 бинарных значений (0 и 1) для проведения статистических тестов.

## 📊 Критерии оценки хакатона

| Критерий | Описание | Статус |
|----------|----------|--------|
| **Инновационность ГСЧ** (30 баллов) | Уникальные источники энтропии, собственные методы обработки | ✅ Реализовано |
| **Архитектура** (3 балла) | Модульность, чистота API, расширяемость | ✅ Реализовано |
| **Качество кода** (6 баллов) | Комментированный код, документация | ✅ Реализовано |
| **Презентация MVP** (10 баллов) | Скринкаст, демонстрация функций | 🔄 В процессе |
| **Готовность решения** (9 баллов) | Стабильность, воспроизводимость | ✅ Реализовано |
| **UX/UI** (12 баллов) | Эстетика, интуитивность, адаптивность | ✅ Реализовано |

## 🔧 API Endpoints

### Аутентификация
- `POST /auth/login` - Авторизация пользователя
- `POST /auth/register` - Регистрация нового пользователя  
- `POST /auth/verify-code` - Подтверждение email
- `GET /auth/get-user-info` - Получение информации о пользователе

### Генерация и анализ
- `POST /video/generate-batch-snapshot` - Генерация случайных чисел
- `POST /files/upload-txt-detailed` - Анализ случайности файлов

## 📈 Производительность

- ⚡ **Быстрая загрузка**: Next.js оптимизация
- 🎯 **SEO-готовность**: Server-side rendering
- 📱 **Адаптивность**: Mobile-first дизайн
- 🔄 **Кэширование**: Оптимизированные запросы

## 🚀 Деплой

### Frontend (Vercel)
```bash
cd generator
vercel --prod
```

### Backend
```bash
cd backend
# Инструкции по деплою backend
```

## 🤝 Команда

- **Frontend**: Next.js разработка, UI/UX дизайн
- **Backend**: API разработка, алгоритмы генерации случайности
- **DevOps**: Инфраструктура, мониторинг

## 🔗 Полезные ссылки

- [Frontend Documentation](generator/README.md)
- [Backend Documentation](backend/README.md)
- [Swagger API Documentation](http://26.237.158.25:8000/docs)
- [NIST Statistical Test Suite](https://csrc.nist.gov/projects/random-bit-generation)


**RandAqua** - где морская жизнь встречается с криптографией! 🐟✨
