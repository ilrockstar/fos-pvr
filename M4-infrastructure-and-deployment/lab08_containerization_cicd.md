# Лабораторная работа 8. Контейнеризация и CI/CD

**Модуль 4.** Инфраструктура и деплой прогнозных систем  
**Время выполнения:** 2 академических часа (+ 4 ч самостоятельной работы)  
**Компетенции:** LC-5.1 (С), LC-5.2 (С)

> **Архитектура A — milestone команды (3–4 чел.):** один репозиторий, общий отчёт. Артефакты: `Dockerfile`, `.github/workflows/`, `DEPLOY.md`. Вес: **5 %** — [docs/grading.md](../docs/grading.md).

---

## Краткая теоретическая справка

**REST API** для сервиса прогноза предоставляет endpoint (например, `POST /predict`), принимает историю ряда или признаки, возвращает JSON с прогнозом и метаданными модели.

**Docker** упаковывает приложение с зависимостями в воспроизводимый образ. **docker-compose** orchestrates сервис + зависимости (Redis, MLflow — опционально).

**CI/CD** автоматизирует сборку, тесты и деплой: при push в репозиторий pipeline запускает lint, unit-тесты API, сборку образа.

**Воспроизводимость:** фиксация версий в `requirements.txt`, переменные окружения для путей к модели, health-check endpoint (`GET /health`).

---

## Задача

Разверните **контейнеризованный сервис прогноза** суточной нагрузки на базе лучшей модели семестра. Настройте минимальный CI/CD pipeline, обеспечивающий автоматическую проверку при изменениях кода.

**Входные данные:** `models/best_model/` (ЛР6), протокол мониторинга (ЛР7).

**Результат:** Docker-образ, REST API, docker-compose, GitHub Actions workflow, инструкция по запуску.

---

## Ход работы

### 1. REST API

1. Создайте FastAPI-приложение (или Flask) с маршрутами:
   - `GET /health` — статус сервиса и версия модели;
   - `POST /predict` — вход: последние $L$ значений ряда (JSON); выход: прогноз на 1 шаг.
2. Валидируйте вход (длина окна, типы, отсутствие NaN).
3. Загрузите модель и scaler при старте приложения.

### 2. Docker-образ

1. Напишите `Dockerfile` (Python 3.10+, slim base image).
2. Скопируйте код, `requirements.txt`, артефакты модели.
3. Соберите образ: `docker build -t load-forecast:latest .`
4. Запустите контейнер и проверьте `/health` и `/predict`.

### 3. docker-compose

1. Опишите сервис `api` с портом 8000.
2. Пробросьте переменные окружения: `MODEL_PATH`, `LOG_LEVEL`.
3. Проверьте запуск: `docker compose up`.

### 4. CI/CD (GitHub Actions)

1. Создайте `.github/workflows/ci.yml`.
2. Триггер: push / pull_request в `main`.
3. Шаги: установка зависимостей → lint (ruff/flake8) → pytest → сборка Docker (без push в registry — достаточно для учебной работы).

### 5. Документация

1. `DEPLOY.md`: как собрать, запустить, пример curl-запроса.
2. Укажите связь с мониторингом из ЛР7 (куда писать prod-метрики).

---

## Фрагменты кода

### FastAPI — скелет сервиса

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, Field
import numpy as np
import pickle

app = FastAPI(title="Load Forecast API", version="1.0.0")

# загрузка при старте — реализуйте сами
model = ...   # load from MODEL_PATH
scaler = ...  # load scaler.pkl

class PredictRequest(BaseModel):
    history: list[float] = Field(..., min_length=14, max_length=60)

class PredictResponse(BaseModel):
    forecast: float
    model_version: str

@app.get("/health")
def health():
    return {"status": "ok", "model_version": "best_model_v1"}

@app.post("/predict", response_model=PredictResponse)
def predict(req: PredictRequest):
    arr = np.array(req.history).reshape(1, -1, 1)
    # масштабирование, inference, денормализация — допишите
    if np.any(np.isnan(arr)):
        raise HTTPException(400, "NaN in history")
    forecast = ...  # float
    return PredictResponse(forecast=forecast, model_version="best_model_v1")
```

### Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app/ ./app/
COPY models/best_model/ ./models/best_model/

ENV MODEL_PATH=/app/models/best_model
EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### docker-compose.yml

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      MODEL_PATH: /app/models/best_model
      LOG_LEVEL: INFO
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 5s
      retries: 3
```

### Пример теста API

```python
# tests/test_api.py
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_health():
    r = client.get("/health")
    assert r.status_code == 200
    assert r.json()["status"] == "ok"

def test_predict():
    payload = {"history": [100.0] * 14}
    r = client.post("/predict", json=payload)
    assert r.status_code == 200
    assert "forecast" in r.json()
```

### GitHub Actions — фрагмент

```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      - run: pip install -r requirements.txt
      - run: pytest tests/ -v
      - run: docker build -t load-forecast:ci .
```

### Пример запроса

```bash
curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"history": [42000, 41500, 43000, 41000, 40500, 39000, 38000,
                 40000, 42000, 43500, 44000, 45000, 46000, 45500]}'
```

---

## Структура проекта (рекомендуемая)

```
ts-forecast-labs/
├── app/
│   └── main.py
├── tests/
│   └── test_api.py
├── models/best_model/
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── DEPLOY.md
└── .github/workflows/ci.yml
```

---

## Формат сдачи

- Репозиторий (или архив) с рабочим Docker-образом.
- `DEPLOY.md` + скриншот/лог успешного CI.
- Демонстрация на защите: curl → JSON-ответ.

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | API `/health` + `/predict` работает локально, Dockerfile собирается, predict возвращает число |
| **Средний** | docker-compose, валидация входа, ≥2 pytest-теста, CI pipeline проходит на push |
| **Продвинутый** | Полная воспроизводимость (requirements pinned), интеграция с мониторингом ЛР7, healthcheck в compose, осмысленная обработка ошибок и версионирование модели |
