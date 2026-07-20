# Практика 3. Мониторинг прогнозной системы

**Модуль 4.** Инфраструктура и деплой прогнозных систем  
**Время:** 2 академических часа (+ 2 ч самостоятельной работы)  
**Комpetенции:** LC-5.1 (С), LC-5.2 (С), ML-5.1 (С)  
**Связь:** [ЛР7](lab07_stability_drift.md), API из [Практики 1–2](practice01_rest_api.md)

---

## Краткая теоретическая справка

**Мониторинг прогнозной системы** — непрерывный контроль того, что сервис **работает** (uptime, latency) и **не деградирует** (рост ошибки, drift).

Уровни:

| Уровень | Что отслеживать |
|---------|-----------------|
| **Infrastructure** | CPU/RAM, restarts, healthcheck |
| **Application** | RPS, latency p95, 4xx/5xx |
| **ML** | MAE/RMSE на скользящем окне, PSI входов, версия модели |
| **Business** | SLA партнёра, алерты при MAPE > порога |

Инструменты (учебный минимум): **структурные логи** (JSON), **metrics endpoint**, **MLflow** (регистрация экспериментов), опционально Prometheus/Grafana.

---

## Задача практики

Добавить к контейнеризованному API **слой observability**: логирование запросов, метрики inference, черновик алертинга. Связать с протоколом drift из ЛР7.

**Результат:** `docs/monitoring.md`, JSON-логи, endpoint `/metrics`, эксперимент в MLflow (или JSONL-лог), симуляция алерта.

---

## Задание 1. Структурное логирование (25 мин)

```python
# app/logging_config.py
import logging
import json
from datetime import datetime, timezone

class JsonFormatter(logging.Formatter):
    def format(self, record):
        log = {
            "ts": datetime.now(timezone.utc).isoformat(),
            "level": record.levelname,
            "msg": record.getMessage(),
        }
        if hasattr(record, "extra_fields"):
            log.update(record.extra_fields)
        return json.dumps(log, ensure_ascii=False)

def setup_logging(level="INFO"):
    handler = logging.StreamHandler()
    handler.setFormatter(JsonFormatter())
    root = logging.getLogger()
    root.handlers = [handler]
    root.setLevel(level)
```

В `/predict` логируйте **без PII**:

```python
import logging
import time

logger = logging.getLogger(__name__)

@app.post("/predict")
def predict(req: PredictRequest):
    t0 = time.perf_counter()
    # ... inference ...
    latency_ms = (time.perf_counter() - t0) * 1000
    logger.info(
        "predict",
        extra={"extra_fields": {
            "event": "predict",
            "history_len": len(req.history),
            "horizon": req.horizon,
            "latency_ms": round(latency_ms, 2),
            "model_version": "best_model_v1",
            # не логируйте полный history в prod без необходимости
        }},
    )
    return ...
```

**Проверка:** 3 запроса → 3 JSON-строки в `docker compose logs`.

---

## Задание 2. Endpoint `/metrics` (25 мин)

In-memory счётчики (учебный вариант; в prod — Prometheus client):

```python
from collections import deque
import statistics

_metrics = {
    "predict_requests_total": 0,
    "predict_errors_total": 0,
    "latency_ms_last": deque(maxlen=100),
}

@app.get("/metrics")
def metrics():
    lat = list(_metrics["latency_ms_last"])
    return {
        "predict_requests_total": _metrics["predict_requests_total"],
        "predict_errors_total": _metrics["predict_errors_total"],
        "latency_ms_p50": statistics.median(lat) if lat else None,
        "latency_ms_p95": sorted(lat)[int(len(lat) * 0.95)] if len(lat) >= 5 else None,
    }
```

Обновляйте счётчики в `/predict` и при ошибках.

**Расширение (средний уровень):** добавьте `rolling_mae_7d` — если есть эталонные фактические значения в sidecar-файле или mock.

---

## Задание 3. MLflow — регистрация модели и сравнение (30 мин)

```python
# scripts/log_model_mlflow.py — запустите один раз при обучении
import mlflow
import json

mlflow.set_experiment("load-forecast-semester")

with mlflow.start_run(run_name="best_model_v1"):
    with open("models/best_model/metrics.json") as f:
        meta = json.load(f)
    mlflow.log_params(meta.get("best_params", {}))
    mlflow.log_metrics({
        "test_mae": meta["test_mae"],
        "test_rmse": meta.get("test_rmse", 0),
    })
    mlflow.log_artifact("models/best_model/metrics.json")
    # mlflow.sklearn.log_model(...) или log artifact .keras
```

На практике:

1. Поднимите MLflow локально: `mlflow ui --port 5000` (или docker-compose service).
2. Залогируйте ≥2 run (baseline vs best после HPO).
3. Скриншот UI: сравнение `test_mae`.

**Без MLflow (допустимо на базовом):** `monitoring/experiments.jsonl` с той же структурой.

---

## Задание 4. Алертинг и playbook (25 мин)

Дополните `docs/monitoring.md` (связь с [ЛР7](lab07_stability_drift.md)):

```markdown
## Пороги алертов (MVP)

| Метрика | Warning | Critical | Действие |
|---------|---------|----------|----------|
| rolling_mae_7d | +15% к baseline | +30% | review → retrain |
| predict_errors_total / min | > 5 | > 20 | page on-call |
| latency_ms p95 | > 500 | > 2000 | scale / profile |
| PSI (inputs) | > 0.1 | > 0.25 | drift investigation |

## Playbook: MAE вырос на 30%
1. Проверить `/health` и версию модели
2. Сравнить распределение входов (PSI) за 7 дней
3. ...
```

**Практическое упражнение «симуляция алерта»:**

```python
# scripts/simulate_degraded_service.py
# отправьте 10 predict с history из «сдвинутого» ряда (+10% level shift)
# покажите рост ошибки vs hold-out baseline из metrics.json
```

Опишите в отчёте: **когда** сработал бы Warning по rolling MAE.

---

## Задание 5. Canary и A/B (обсуждение + мини-задание, 15 мин)

**Теория (5 мин на паре):** canary — 5 % трафика на `model_v2`, 95 % на `model_v1`.

**Мини-реализация** — query-параметр:

```python
@app.post("/predict")
def predict(req: PredictRequest, model_variant: str = "v1"):
    if model_variant not in ("v1", "v2"):
        raise HTTPException(400, "unknown variant")
    # route to model registry[model_variant]
    ...
```

В отчёте: 3 предложения — когда canary **оправдан**, когда достаточно big-bang deploy.

---

## Задание 6. docker-compose с MLflow (опционально, продвинутый)

```yaml
services:
  api:
    build: .
    ports: ["8000:8000"]
    depends_on: [mlflow]
  mlflow:
    image: ghcr.io/mlflow/mlflow:v2.14.0
    command: mlflow server --host 0.0.0.0 --port 5000
    ports: ["5000:5000"]
    volumes: ["./mlruns:/mlflow/mlruns"]
```

---

## Формат сдачи

- Код: logging, `/metrics`, (опционально) MLflow script
- `docs/monitoring.md` — пороги + playbook (1–2 стр.)
- Скрины: JSON-лог, `/metrics` response, MLflow UI
- Краткий отчёт: симуляция алерта (½ стр.)

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | JSON-логи predict, `/metrics` с counters, monitoring.md с порогами |
| **Средний** | latency p50/p95, MLflow ≥2 runs или experiments.jsonl, симуляция drift/MAE |
| **Продвинутый** | Playbook связан с ЛР7 (PSI/retrain), canary через variant, compose + MLflow |

---

## Связь с итоговой защитой (ЛР8 / PRC)

На защите покажите **единый сценарий**:

1. `docker compose up`
2. `POST /predict` → JSON-лог в stdout
3. `GET /metrics` → counters выросли
4. Открыть MLflow → версия модели совпадает с `/model/info`
5. Устно: «что произойдёт при drift» → playbook п.1–3
