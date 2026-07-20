# Практика 1. Проектирование REST API

**Модуль 4.** Инфраструктура и деплой прогнозных систем  
**Время:** 1 академический час (+ 1 ч самостоятельной работы)  
**Комpetенции:** LC-5.1 (С)  
**Связь:** подготовка к [ЛР8](lab08_containerization_cicd.md); модель из [ЛР6](../M3-neural-networks/lab06_validation_hyperparameters.md)

---

## Краткая теоретическая справка

**REST API** — интерфейс прогнозного сервиса для клиентов (дашборд, ERP, микросервис планирования). Ключевые принципы:

- **Ресурсы** — `/health`, `/predict`, `/model/info`;
- **HTTP-методы** — GET для статуса, POST для inference;
- **Контракт** — явный JSON-schema запроса/ответа, коды ошибок (400, 422, 503);
- **Валидация** — длина окна, типы, NaN, границы значений;
- **Идempotентность** — повторный запрос с теми же данными даёт тот же прогноз (при фиксированной версии модели).

Проектирование API **начинается с контракта**, а не с кода — это снижает число переделок при интеграции с партнёром.

---

## Задача практики

Спроектировать и реализовать **минимальный REST API** сервиса прогноза суточной нагрузки. На занятии — контракт и два endpoint; дома — валидация, обработка ошибок и автотесты.

**Результат:** работающий FastAPI-сервис локально (`uvicorn`), файл `docs/api_contract.md`, ≥2 pytest-теста.

---

## Задание 1. Проектирование контракта (20 мин)

Заполните спецификацию в `docs/api_contract.md`:

| Endpoint | Метод | Назначение |
|----------|-------|------------|
| `/health` | GET | Жив ли сервис, версия модели |
| `/predict` | POST | Прогноз на 1 шаг |
| `/model/info` | GET | Метаданные: lookback, метрики test, дата обучения |

Для **`POST /predict`** опишите:

```json
// Request — заполните поля и ограничения
{
  "history": [/* ... */],
  "horizon": 1
}

// Response 200
{
  "forecast": 0.0,
  "model_version": "",
  "generated_at": "ISO-8601"
}

// Response 422 — пример тела ошибки
```

**Вопросы для контракта (ответьте в md):**

1. Минимальная и максимальная длина `history` (из `hparams.json` ЛР6)?
2. Что вернуть, если модель ещё не загружена (503)?
3. Нужен ли отдельный endpoint для batch-прогноза — аргументируйте в 2 предложениях.

---

## Задание 2. Реализация `/health` и `/model/info` (15 мин)

```python
# app/main.py — дополните
from fastapi import FastAPI
from datetime import datetime, timezone
import json
import os

app = FastAPI(title="Load Forecast API", version="1.0.0")

MODEL_META_PATH = os.getenv("MODEL_META_PATH", "models/best_model/metrics.json")

@app.get("/health")
def health():
    # status: ok | degraded | unavailable
    # проверьте наличие файлов модели на диске
    ...

@app.get("/model/info")
def model_info():
    # загрузите metrics.json / hparams.json
    ...
```

Проверка:

```bash
uvicorn app.main:app --reload --port 8000
curl http://localhost:8000/health
curl http://localhost:8000/model/info
```

---

## Задание 3. Реализация `/predict` с валидацией (25 мин)

```python
from fastapi import HTTPException
from pydantic import BaseModel, Field, field_validator

class PredictRequest(BaseModel):
    history: list[float] = Field(..., min_length=14, max_length=60)
    horizon: int = Field(1, ge=1, le=7)

    @field_validator("history")
    @classmethod
    def no_nan(cls, v):
        if any(x != x for x in v):  # NaN check
            raise ValueError("NaN in history")
        return v

@app.post("/predict")
def predict(req: PredictRequest):
    if req.horizon > 1:
        raise HTTPException(501, "Multi-step not implemented in MVP")
    # TODO: load model, scaler, inference, inverse_transform
    ...
```

**Сценарии для ручной проверки:**

| Запрос | Ожидаемый код |
|--------|---------------|
| 14 валидных значений | 200 |
| 5 значений (короткая history) | 422 |
| history с `null`/NaN | 422 |
| `horizon=3` (если не реализовано) | 501 |

---

## Задание 4. Тесты API (дома, 30 мин)

```python
# tests/test_api.py
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_health_returns_ok():
    r = client.get("/health")
    assert r.status_code == 200
    assert "status" in r.json()

def test_predict_valid_payload():
    payload = {"history": [45000.0] * 14, "horizon": 1}
    r = client.post("/predict", json=payload)
    assert r.status_code == 200
    assert r.json()["forecast"] > 0

def test_predict_short_history_422():
    r = client.post("/predict", json={"history": [1.0, 2.0]})
    assert r.status_code == 422
```

Добавьте **свой** тест на граничный случай (пустой список, отрицательные значения и т.п.).

---

## Задание 5. Документация OpenAPI (10 мин)

FastAPI генерирует Swagger UI: `http://localhost:8000/docs`.

1. Сделайте скриншот или экспорт `openapi.json`.
2. Сверьте с `docs/api_contract.md` — есть ли расхождения?
3. Добавьте `description` к endpoint через docstring или `Field(description=...)`.

---

## Формат сдачи

- `app/main.py`, `docs/api_contract.md`, `tests/test_api.py`
- Краткий отчёт (½ стр.): 3 решения проектирования и 1 компромисс MVP

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | `/health` + `/predict` работают, контракт заполнен, 2 теста проходят |
| **Средний** | Валидация 422 на короткую history, `/model/info`, OpenAPI сверен с контрактом |
| **Продвинутый** | Осмысленные коды ошибок (503 при отсутствии модели), 4+ теста, horizon/batch обоснован в контракте |
