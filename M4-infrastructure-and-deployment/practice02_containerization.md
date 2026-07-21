# Практика 2. Контейнеризация и воспроизводимость

**Модуль 4.** Инфраструктура и деплой прогнозных систем  
**Время:** 1 академический час (+ 1 ч самостоятельной работы)  
**Комpetенции:** LC-5.2 (С)  
**Связь:** [Практика 1](practice01_rest_api.md) → [ЛР9](lab09_containerization_cicd.md)

---

## Краткая теоретическая справка

**Контейнер** упаковывает приложение с зависимостями в изолированный образ. **Воспроизводимость** означает: образ, собранный сегодня на ноутбуке студента и на CI-сервере, даёт **одинаковое** поведение API при тех же env-переменных.

Практики воспроизводимости:

- **Pinning** версий в `requirements.txt` (`package==1.2.3`);
- **Многоstage Dockerfile** — отделение сборки и runtime (опционально);
- **`.dockerignore`** — не копировать `.git`, `data/raw`, notebooks;
- **ENV** для путей: `MODEL_PATH`, `LOG_LEVEL`, не хардкод в коде;
- **Иммутабельные теги** образов: `load-forecast:1.0.0`, не только `:latest`.

---

## Задача практики

Упаковать API из Практики 1 в **Docker-образ**, обеспечить воспроизводимый запуск через **docker-compose** и проверить, что прогноз совпадает с локальным запуском.

**Результат:** `Dockerfile`, `docker-compose.yml`, `.dockerignore`, `DEPLOY.md`, скриншот успешного `curl /predict` из контейнера.

---

## Задание 1. Подготовка зависимостей (15 мин)

Зафиксируйте версии:

```bash
pip freeze | grep -E "fastapi|uvicorn|pydantic|numpy|pandas|scikit-learn|tensorflow" > requirements.txt
# или вручную: fastapi==0.115.0, uvicorn[standard]==0.30.0, ...
```

Создайте `.dockerignore`:

```
.git
__pycache__
*.pyc
.env
data/raw/
notebooks/
.pytest_cache
*.md
!DEPLOY.md
```

**Чек-лист воспроизводимости** (отметьте в отчёте):

- [ ] Версии Python указаны в Dockerfile (`FROM python:3.11-slim`)
- [ ] `requirements.txt` без «голых» имён пакетов
- [ ] Модель копируется явным путём, не скачивается при build из интернета

---

## Задание 2. Dockerfile (20 мин)

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# системные зависимости при необходимости (curl для healthcheck)
RUN apt-get update && apt-get install -y --no-install-recommends curl \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app/ ./app/
COPY models/best_model/ ./models/best_model/

ENV MODEL_PATH=/app/models/best_model
ENV MODEL_META_PATH=/app/models/best_model/metrics.json
ENV PYTHONUNBUFFERED=1

EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD curl -f http://localhost:8000/health || exit 1

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Сборка и запуск:

```bash
docker build -t load-forecast:1.0.0 .
docker run --rm -p 8000:8000 load-forecast:1.0.0
```

**Задание:** сравните прогноз **локально** и **из контейнера** на одном payload — значения должны совпадать (± float epsilon).

---

## Задание 3. docker-compose (15 мин)

```yaml
# docker-compose.yml
services:
  api:
    build: .
    image: load-forecast:1.0.0
    ports:
      - "8000:8000"
    environment:
      MODEL_PATH: /app/models/best_model
      LOG_LEVEL: INFO
    restart: unless-stopped
    # опционально: volumes для hot-reload модели без rebuild
    # volumes:
    #   - ./models/best_model:/app/models/best_model:ro
```

Проверка:

```bash
docker compose up --build -d
docker compose ps
docker compose logs api --tail 20
curl http://localhost:8000/health
docker compose down
```

---

## Задание 4. Переменные окружения и секреты (10 мин)

1. Вынесите `LOG_LEVEL` и `MODEL_PATH` в `.env.example` (без секретов):

```env
MODEL_PATH=/app/models/best_model
LOG_LEVEL=INFO
```

2. Добавьте `.env` в `.gitignore`.
3. В `DEPLOY.md` опишите: какие переменные **обязательны**, что будет, если `MODEL_PATH` указывает на пустую папку.

```python
# фрагмент: fail fast при старте
@app.on_event("startup")
def load_artifacts():
    path = os.getenv("MODEL_PATH")
    if not os.path.isdir(path):
        raise RuntimeError(f"MODEL_PATH not found: {path}")
    # загрузка model, scaler
```

---

## Задание 5. Проверка воспроизводимости (дома, 20 мин)

Выполните **чек-лист «другая машина»**:

1. Клонировать репозиторий на другой ПК / одногруппнику (или `docker system prune` + чистая сборка).
2. Только команды из `DEPLOY.md` — без ручного `pip install`.
3. Зафиксировать в отчёте: ОС, `docker --version`, время сборки, SHA256 образа (`docker inspect --format='{{.Id}}' load-forecast:1.0.0`).

**Вопрос для отчёта:** что сломается, если в `requirements.txt` убрать pin версии `numpy`?

---

## Формат сдачи

- `Dockerfile`, `docker-compose.yml`, `.dockerignore`, `.env.example`, `DEPLOY.md`
- Таблица: локальный vs container predict (1 строка)
- Скрин `docker compose ps` со status **healthy**

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | `docker build` успешен, `/health` из контейнера, DEPLOY.md с командами |
| **Средний** | docker-compose, pinned requirements, прогноз совпадает с local, .dockerignore |
| **Продвинутый** | HEALTHCHECK, startup validation MODEL_PATH, reproducibility checklist пройден, тег версии образа |
