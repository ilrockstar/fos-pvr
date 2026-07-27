# Лабораторная работа 9. Проектирование REST API. Контейнеризация и CI/CD

**Модуль 4.** Инфраструктура и деплой прогнозных систем  
**Ролевой фокус:** ML Engineer, MLOps  
**Компетенции:** LC-5.1, LC-5.2 (С–П)  
**Время:** 1 ч лекция + 1 ч лабораторная (+ 4 ч самостоятельной работы; активное использование пререквизитов по Docker)

> **Milestone команды (3–4 чел.):** REST API, `Dockerfile`, `docker-compose.yml`, интеграция **MLflow Model Registry**, `DEPLOY.md`. Вес: **4 %** — [docs/grading.md](../docs/grading.md).

---

## 1. Цель работы

Научиться упаковывать обученные прогнозные модели (из модулей 2–3) в легковесный Docker-контейнер, проектировать REST API с валидацией входящих данных и настраивать автоматический трекинг экспериментов в MLflow.

---

## 2. Входные требования и пререквизиты

- Остаточные знания по дисциплинам «Веб-программирование» и «Основы контейнеризации»: синтаксис Dockerfile, HTTP REST (GET/POST).
- В качестве основы — лучшая модель из [ЛР6–8](../M3-neural-networks/lab08_validation_drift.md): ARIMA, boosting или LSTM; артефакт `models/best_model/`.

**Стек:** FastAPI, Pydantic, Docker / docker-compose, MLflow (инференс — statsmodels / sklearn / PyTorch по типу модели команды).

---

## 3. Задание

1. Разработать сервис на **FastAPI** с эндпоинтом `/predict`: JSON-запрос с историческим окном → валидация через **Pydantic** → прогноз на заданный горизонт (например, 24 часа).
2. Написать **Dockerfile** (желательно multi-stage) для CPU-инференса.
3. Интегрировать **MLflow Model Registry**: при старте контейнера загружать веса верифицированной модели по run ID / stage.
4. *(Рекомендуется)* Настроить **CI** (`.github/workflows/ci.yml`): lint → pytest → docker build.

**Ожидаемые артефакты:** REST API, `Dockerfile`, `docker-compose.yml`, `services/ml_service/` (или `app/`), логи MLflow, `DEPLOY.md`, раздел отчёта `docs/report/09_deploy.md`.

---

## 4. Теоретическая справка

### 4.1. Инференс (Inference)

Этап применения обученной модели к новым данным:

1. Приём HTTP-запроса с историческими данными.
2. Загрузка модели (из MLflow или локального файла).
3. Выполнение прогноза (`model.forecast()`, `model.predict()` и т.п.).
4. Возврат результата в JSON.

### 4.2. REST API прогнозного сервиса

| Эндпоинт | Метод | Назначение |
|----------|-------|------------|
| `/health` | GET | Проверка работоспособности сервиса |
| `/predict` | POST | Прогноз на основе исторических данных |
| `/metrics` | GET | Текущие метрики качества модели *(опционально, доработка в ЛР10)* |
| `/docs` | GET | Swagger-документация (автоматически) |

### 4.3. Контейнеризация

Docker упаковывает сервис со всеми зависимостями. Это даёт воспроизводимость, изоляцию окружения и простой деплой.

---

## 5. Ход работы

### Этап 1. Структура проекта

```
team-<name>/
├── services/
│   └── ml_service/
│       ├── main.py              # Точка входа FastAPI
│       ├── api_handler.py       # Класс-обработчик запросов
│       ├── schemas.py           # Pydantic-схемы
│       ├── config.py            # Конфигурация
│       ├── Dockerfile
│       ├── requirements.txt
│       └── .env.example
├── models/
│   ├── get_model.py             # Загрузка модели из MLflow
│   └── best_model/              # из ЛР8
├── docker-compose.yml
├── DEPLOY.md
└── docs/report/09_deploy.md
```

> Команда может использовать `app/` вместо `services/ml_service/`, если структура уже зафиксирована в уставе — главное, чтобы пути были документированы в `DEPLOY.md`.

### Этап 2. REST API

#### 2.1. Pydantic-схемы (`schemas.py`)

```python
from pydantic import BaseModel, Field, field_validator
from typing import List, Optional
from datetime import datetime

class PredictionRequest(BaseModel):
    """Запрос на получение прогноза."""
    history: List[float] = Field(..., description="Исторические значения ряда")
    horizon: int = Field(..., ge=1, le=30, description="Горизонт прогнозирования (1–30)")
    date_start: Optional[str] = Field(None, description="Дата начала (YYYY-MM-DD)")

    @field_validator("history")
    @classmethod
    def check_history_length(cls, v):
        if len(v) < 10:
            raise ValueError("Длина истории должна быть не менее 10 значений")
        return v

class PredictionResponse(BaseModel):
    """Ответ сервиса с прогнозом."""
    predictions: List[float]
    confidence_intervals: Optional[List[dict]] = None
    model_version: str
    timestamp: datetime
```

#### 2.2. Обработчик модели (`api_handler.py`)

```python
import pickle
import logging
from pathlib import Path

logger = logging.getLogger(__name__)

class ModelHandler:
    """Загрузка и использование модели прогнозирования."""

    def __init__(self, model_path: str = "/models/best_model/model.pkl"):
        self.model_path = Path(model_path)
        self.model = None
        self.model_version = "unknown"
        self._load_model()

    def _load_model(self):
        if not self.model_path.exists():
            raise FileNotFoundError(f"Модель не найдена: {self.model_path}")
        with open(self.model_path, "rb") as f:
            self.model = pickle.load(f)
        logger.info("Модель загружена из %s", self.model_path)

    def predict(self, history: list, horizon: int) -> list:
        # Адаптируйте под вашу модель (SARIMA / XGBoost / LSTM)
        # predictions = self.model.forecast(steps=horizon)
        # return predictions.tolist()
        raise NotImplementedError("Реализуйте inference для вашей модели")
```

#### 2.3. FastAPI-приложение (`main.py`)

```python
from fastapi import FastAPI, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from datetime import datetime
import logging

from schemas import PredictionRequest, PredictionResponse
from api_handler import ModelHandler

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

app = FastAPI(
    title="Time Series Forecasting API",
    description="Сервис прогнозирования временных рядов",
    version="1.0.0",
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

try:
    handler = ModelHandler()
except Exception as e:
    logger.error("Ошибка загрузки модели: %s", e)
    handler = None

@app.get("/health")
def health():
    return {"status": "healthy", "model_loaded": handler is not None}

@app.post("/predict", response_model=PredictionResponse)
def predict(request: PredictionRequest):
    if handler is None:
        raise HTTPException(status_code=503, detail="Модель не загружена")
    try:
        predictions = handler.predict(request.history, request.horizon)
        return PredictionResponse(
            predictions=predictions,
            model_version="v1.0.0",
            timestamp=datetime.now(),
        )
    except Exception as e:
        logger.error("Ошибка прогноза: %s", e)
        raise HTTPException(status_code=500, detail=str(e)) from e
```

### Этап 3. Загрузка модели из MLflow

`models/get_model.py`:

```python
import mlflow
import pickle

MLFLOW_TRACKING_URI = "http://localhost:5000"
RUN_ID = "ваш_run_id"  # из MLflow UI или ЛР8

def download_model(run_id: str, output_path: str = "models/best_model/model.pkl"):
    mlflow.set_tracking_uri(MLFLOW_TRACKING_URI)
    model = mlflow.sklearn.load_model(f"runs:/{run_id}/model")
    with open(output_path, "wb") as f:
        pickle.dump(model, f)
    print(f"Модель сохранена в {output_path}")

if __name__ == "__main__":
    download_model(RUN_ID)
```

### Этап 4. Контейнеризация

**Dockerfile** (базовый; для «Продвинутого» — multi-stage):

```dockerfile
FROM python:3.11-slim

WORKDIR /ml_service

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

ENV MODEL_PATH=/models/best_model/model.pkl

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**docker-compose.yml:**

```yaml
version: "3.8"

services:
  forecast-api:
    build:
      context: ./services/ml_service
      dockerfile: Dockerfile
    container_name: forecast-service
    ports:
      - "8000:8000"
    volumes:
      - ./models:/models
    environment:
      - MODEL_PATH=/models/best_model/model.pkl
      - MLFLOW_TRACKING_URI=http://host.docker.internal:5000
    restart: unless-stopped
```

**requirements.txt** (минимум):

```
fastapi>=0.104.1
uvicorn[standard]>=0.24.0
pydantic>=2.5.0
pandas>=2.1.0
numpy>=1.24.0
scikit-learn>=1.3.0
mlflow>=2.8.0
```

### Этап 5. Запуск и проверка

```bash
docker compose up -d --build

curl http://localhost:8000/health

curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"history": [100, 102, 101, 105, 107, 106, 110, 112, 111, 115], "horizon": 5}'
```

### Этап 6. CI/CD *(рекомендуется для уровня «Продвинутый»)*

`.github/workflows/ci.yml`: lint (ruff/flake8) → pytest → `docker build`. Связь с playbook retraining из [ЛР8](../M3-neural-networks/lab08_validation_drift.md).

---

## 6. Требования к отчёту

Раздел `docs/report/09_deploy.md` (3–5 стр.) или общий [шаблон milestone](../Project/templates/milestone_report.md):

1. Структура проекта (дерево директорий).
2. Спецификация API: эндпоинты, форматы, примеры запросов/ответов.
3. Фрагменты кода: `main.py`, `schemas.py`, `api_handler.py`.
4. `Dockerfile` и `docker-compose.yml` с комментариями.
5. Инструкция по запуску (сборка, smoke-test ≤ 5 мин).
6. Скриншоты: успешные запросы, Swagger `/docs`.
7. Выводы: готовность к [ЛР10](lab10_monitoring.md).

---

## 7. Критерии оценки

### 7.1. Детализированная шкала (сумма = 4 % milestone)

| Критерий | Макс. балл |
|----------|------------|
| REST API (`/health`, `/predict`, `/`) | 0,5 |
| Валидация входа (Pydantic) | 0,4 |
| Интеграция MLflow Model Registry | 0,5 |
| Dockerfile и docker-compose работают | 0,5 |
| Скорость инференса, отсутствие утечек памяти | 0,4 |
| Документация API (Swagger) | 0,4 |
| Качество отчёта и защита | 1,2 |
| **Итого** | **4,0** |

### 7.2. Уровни Б / С / П (ориентир для milestone)

| Уровень | Критерии |
|---------|----------|
| **Базовый** | API + Docker; `/health` и `/predict` работают локально |
| **Средний** | MLflow run зарегистрирован; `DEPLOY.md`; Swagger доступен |
| **Продвинутый** | Multi-stage Dockerfile; CI green; demo без ручных правок на защите |

---

## 8. Контрольные вопросы

### Блок 1. Архитектура и проектирование REST API

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 1 | Что такое REST API? Какие основные принципы REST вы знаете? |
| 2 | Какие эндпоинты вы реализовали в своём сервисе? Каково их назначение? |
| 3 | Какие HTTP-методы вы использовали и почему? |
| 4 | Что такое валидация входных данных? Как вы её реализовали? |
| 5 | Какие форматы данных вы использовали для запросов и ответов? Почему? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 6 | Что такое Swagger / OpenAPI? Как вы настроили документацию? |
| 7 | Как вы обрабатываете ошибки в API? Какие HTTP-статусы возвращаете? |
| 8 | Что такое CORS? Зачем вы добавили CORS-middleware? |
| 9 | Как вы организовали загрузку модели в сервисе? Какие есть подходы? |
| 10 | Как вы тестировали API? Какие инструменты использовали? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 11 | Как обеспечить асинхронную обработку запросов в FastAPI? Какие преимущества это даёт? |
| 12 | Что такое dependency injection? Как вы его использовали? |
| 13 | Как организовать версионирование API? Какие стратегии вы знаете? |
| 14 | Как бы вы добавили обработку batch-запросов (много прогнозов за раз)? |
| 15 | Как вы обеспечиваете graceful shutdown сервиса? |

### Блок 2. Контейнеризация (Docker)

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 16 | Что такое Docker? Для чего он используется? |
| 17 | Что такое Dockerfile? Какие основные инструкции вы использовали? |
| 18 | Что такое docker-compose? Зачем он нужен? |
| 19 | Какие переменные окружения вы использовали в контейнере? |
| 20 | Как пробрасывали модель в контейнер? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 21 | В чём отличие Docker-образа от Docker-контейнера? |
| 22 | Как вы оптимизировали размер Docker-образа? |
| 23 | Что такое многоступенчатая сборка (multi-stage build)? |
| 24 | Как вы управляете зависимостями в контейнере? |
| 25 | Как настроить сборку для CPU и GPU в одном Dockerfile? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 26 | Что такое `ENTRYPOINT` и `CMD` в Docker? В чём разница? |
| 27 | Как обеспечить healthcheck для Docker-контейнера? |
| 28 | Как вы решали проблему с системными зависимостями (например, для pandas)? |
| 29 | Как организовать логирование внутри контейнера для централизованного сбора? |
| 30 | Как вы проверяли воспроизводимость сборки Docker-образа? |

---

## 9. Связь с другими элементами ФОС

| Элемент | Связь |
|---------|-------|
| [ЛР8](../M3-neural-networks/lab08_validation_drift.md) | Модель `best_model`, MLflow run |
| [ЛР10](lab10_monitoring.md) | Сервис из этой ЛР — основа для мониторинга |
| [PRC](../Project/project_guidelines.md) | Live demo MVP на защите |
