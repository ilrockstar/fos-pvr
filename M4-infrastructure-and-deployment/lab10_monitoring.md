# Лабораторная работа 10. Мониторинг, дрейф данных и автоматический ретренинг

**Модуль 4.** Инфраструктура и деплой прогнозных систем  
**Ролевой фокус:** ML Researcher, MLOps  
**Комpetence:** LC-5.1, LC-5.2, ML-5.1, ML-5.2, ML-2.2 (С–П)  
**Время:** 1 ч лекция + 2 ч лабораторная (+ 4 ч самостоятельной работы)

> **Milestone команды (3–4 чел.):** мониторинг качества, детекция drift (Evidently), дашборд, пайплайн ретренинга (Airflow / Prefect), `docs/monitoring.md`. Вес: **4 %** — [docs/grading.md](../docs/grading.md).  
> Источник методики: [lr10.pdf](../lr10.pdf)

---

## 1. Цель работы

Освоить методы промышленного контроля устойчивости прогнозных систем: выявление **Data / Concept Drift**, визуализация метрик и автоматизация дообучения модели при деградации качества.

---

## 2. Входные требования и пререквизиты

- Базовые знания SQL (агрегация и фильтрация логов) — для анализа production-логов.
- Развёрнутый в [ЛР9](lab09_containerization_cicd.md) контейнеризованный сервис прогнозирования.
- Правила drift и baseline-метрики из [ЛР8](../M3-neural-networks/lab08_validation_drift.md).

**Стек:** Evidently AI (или Whylogs), Apache Airflow или Prefect, MLflow; опционально Prometheus + Grafana.

---

## 3. Задание

1. **Метрики:** интегрировать логирование входящих запросов и ответов модели.
2. **Детекция дрейфа:** Evidently AI — Data Drift (KS-тест для числовых признаков) и Concept Drift (деградация MAE/MAPE на верифицированных окнах).
3. **Дашборд:** Evidently UI *(рекомендуется)* или Grafana.
4. **Авто-ретренинг:** Airflow DAG или Prefect flow — по триггеру или расписанию переобучение на новых данных, walk-forward validation, обновление тега в MLflow Registry.
5. **Симуляция:** скрипт аномалий для проверки пайплайна (связь с [ПЗ5](pz05_break_the_model.md)).

**Ожидаемые артефакты:** `monitoring/quality_monitor.py`, `monitoring/drift_detector.py`, конфигурация дашборда, DAG/flow ретренинга, `docs/monitoring.md`, `docs/report/10_monitoring.md`.

---

## 4. Теоретическая справка

### 4.1. Дрейф данных

| Тип | Описание |
|-----|----------|
| **Data drift** | Изменение распределения входных признаков (например, средний уровень нагрузки вырос) |
| **Concept drift** | Изменение связи признаков с целевой переменной (изменились влияющие факторы) |

Инструменты: Evidently AI, Alibi-detect, правила из `src/drift_analysis.py` (ЛР8).

### 4.2. Ретренинг

- **Периодический** — еженедельно / ежемесячно.
- **По триггеру** — при обнаружении drift или росте MAE.

Оркестрация: Apache Airflow, Prefect.

### 4.3. MLflow

- Логирование экспериментов (params, metrics, artifacts).
- Model Registry: Staging → Production.
- Сравнение версий моделей.

---

## 5. Ход работы

### Этап 1. Настройка MLflow

```bash
mlflow server --host 0.0.0.0 --port 5000 \
  --backend-store-uri sqlite:///mlflow.db \
  --default-artifact-root ./mlflow_artifacts
```

Логирование эксперимента (если не сделано в ЛР8–9):

```python
import mlflow
from mlflow.models.signature import infer_signature

mlflow.set_tracking_uri("http://localhost:5000")
mlflow.set_experiment("forecast_experiment")

with mlflow.start_run(run_name="v1.0.0") as run:
    mlflow.log_param("model_type", "SARIMA")
    mlflow.log_metric("rmse", 15.3)
    mlflow.log_metric("mae", 12.1)
    mlflow.sklearn.log_model(
        sk_model=model,
        artifact_path="model",
        signature=infer_signature(X_test, y_pred),
        registered_model_name="forecast_model",
    )
```

### Этап 2. Мониторинг качества

`monitoring/quality_monitor.py`:

```python
import pickle
import numpy as np
import pandas as pd
from sklearn.metrics import mean_absolute_error, mean_squared_error

class QualityMonitor:
    def __init__(self, model_path: str, test_data_path: str):
        with open(model_path, "rb") as f:
            self.model = pickle.load(f)
        self.data = pd.read_csv(test_data_path)

    def evaluate(self, horizon: int = 7) -> dict:
        y_true = self.data["actual"].iloc[-horizon:]
        y_pred = self.model.forecast(steps=horizon)
        return {
            "mae": mean_absolute_error(y_true, y_pred),
            "rmse": float(np.sqrt(mean_squared_error(y_true, y_pred))),
            "mape": float(np.mean(np.abs((y_true - y_pred) / y_true)) * 100),
        }
```

Интегрируйте логирование запросов/ответов API из [ЛР9](lab09_containerization_cicd.md): timestamp, latency, `model_version`, status.

### Этап 3. Обнаружение дрейфа (Evidently)

```bash
pip install evidently
```

`monitoring/drift_detector.py`:

```python
import json
import pandas as pd
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset, DataQualityPreset

def generate_drift_snapshot(
    reference_data: pd.DataFrame,
    current_data: pd.DataFrame,
    output_path: str = "snapshots/drift.json",
) -> dict:
    drift_report = Report(metrics=[DataDriftPreset(), DataQualityPreset()])
    drift_report.run(reference_data=reference_data, current_data=current_data)
    drift_report.save_json(output_path)
    drift_report.save_html("drift_report.html")

    result = drift_report.as_dict()
    drift_share = result["metrics"][0]["result"]["drift_share"]
    return {"drift_share": drift_share}
```

Reference = train / baseline из ЛР1–2; current = симулированный сдвиг или свежие данные.

### Этап 4. Дашборд мониторинга

**Вариант А — Evidently UI** *(рекомендуется)*

```bash
pip install "evidently[ui]"
evidently ui --host 0.0.0.0 --port 8080
```

Настройте проект, вкладки Data Drift и Data Quality; загружайте JSON-снапшоты из `snapshots/`.

**Вариант Б — Grafana + Prometheus** *(продвинутый)*

Используйте `docker-compose.monitoring.yml` с сервисами evidently-service, prometheus, grafana (см. [lr10.pdf](../lr10.pdf), §4).

### Этап 5. Пайплайн ретренинга (Prefect / Airflow)

Пример на **Prefect**:

```python
from datetime import datetime, timedelta
from prefect import flow, task

@task
def check_drift_and_update_dashboard() -> bool:
    reference = pd.read_csv("data/reference.csv")
    current = pd.read_csv("data/current.csv")
    metrics = generate_drift_snapshot(reference, current)
    return metrics["drift_share"] > 0.3

@task
def retrain_model() -> str:
    # Вызов скриптов обучения команды (ЛР3–7)
    return "models/best_model/model_v2.pkl"

@task
def deploy_model(model_path: str) -> str:
    # Обновление MODEL_PATH в сервисе ЛР9 + MLflow stage Production
    return "deployed"

@flow
def ml_monitoring_pipeline():
    if check_drift_and_update_dashboard():
        model = retrain_model()
        deploy_model(model)
    else:
        print("Дрейф не обнаружен.")
```

Для **Airflow** — аналогичный DAG: `fetch_data` → `check_drift` → `train` → `validate` → `register_model` → `deploy`.

Пороги из ЛР8 (ориентир): PSI > 0,2, MAE +20 % vs baseline → trigger retrain.

### Этап 6. Симуляция деградации

Скрипт `monitoring/simulate_drift.py` — сдвиг среднего / дисперсии / сезонности. Результаты используйте в [ПЗ5](pz05_break_the_model.md) и для demo на PRC.

---

## 6. Требования к отчёту

Раздел `docs/report/10_monitoring.md`:

1. MLflow: скриншоты UI, Model Registry.
2. Скрипты мониторинга качества (MAE/RMSE на новых данных).
3. Демонстрация drift: скриншоты Evidently (HTML / UI).
4. Дашборд: вкладки Data Drift, Data Quality, графики метрик.
5. Код пайплайна ретренинга с комментариями.
6. Демонстрация запуска ретренинга (логи, обновление модели в API).
7. Выводы по устойчивости и playbook retraining.

---

## 7. Критерии оценки

### 7.1. Детализированная шкала (сумма = 4 % milestone)

| Критерий | Макс. балл |
|----------|------------|
| MLflow (логирование, регистрация модели) | 0,6 |
| Мониторинг качества (скрипт метрик) | 0,6 |
| Детекция дрейфа (Evidently) | 0,6 |
| Дашборд (Evidently UI / Grafana) | 0,7 |
| Пайплайн ретренинга (Airflow / Prefect) | 0,7 |
| Интеграция мониторинга в сервис | 0,4 |
| Качество отчёта и защита | 0,4 |
| **Итого** | **4,0** |

### 7.2. Уровни Б / С / П

| Уровень | Критерии |
|---------|----------|
| **Базовый** | QualityMonitor + `monitoring.md` с порогами; drift-report HTML |
| **Средний** | Evidently snapshots; симуляция алерта; playbook retraining |
| **Продвинутый** | Дашборд + автоматический ретренинг; обновление модели в API без ручных правок |

---

## 8. Связь с ПЗ5 «Сломай модель»

| Аспект | ЛР10 | [ПЗ5](pz05_break_the_model.md) |
|--------|------|--------------------------------|
| Фокус | Инфраструктура мониторинга и ретренинга | Анализ устойчивости модели |
| Результат | Работающая система мониторинга | Сценарий деградации + стратегия восстановления |
| Зависимость | Использует сценарии ПЗ5 для тестирования | Сценарий drift проверяется через мониторинг ЛР10 |

---

## 9. Контрольные вопросы

### Блок 3. MLflow и управление моделями

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 31 | Что такое MLflow? Какие компоненты вы использовали? |
| 32 | Как вы логировали эксперименты в MLflow? |
| 33 | Что такое Model Registry? Зачем он нужен? |
| 34 | Как вы регистрировали модель в Model Registry? |
| 35 | Как вы загружали модель из MLflow для инференса? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 36 | Как вы сравнивали эксперименты в MLflow? |
| 37 | Как организовать переход модели между стадиями (Staging → Production)? |
| 38 | Что такое артефакты в MLflow? Какие артефакты вы сохраняли? |
| 39 | Как вы настраивали backend хранения для MLflow? |
| 40 | Как вы интегрировали MLflow с вашим API? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 41 | Как организовать A/B-тестирование двух версий моделей с MLflow? |
| 42 | Как настроить MLflow с удалённым хранилищем артефактов (S3 / MinIO)? |
| 43 | Как реализовать автоматический выбор лучшей модели по метрике? |
| 44 | Как гарантировать воспроизводимость эксперимента в MLflow? |
| 45 | Как организовать мониторинг метрик модели в продуктиве? |

### Блок 4. Мониторинг и дрейф

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 46 | Что такое data drift и concept drift? Приведите примеры. |
| 47 | Какие метрики качества прогноза вы мониторили? |
| 48 | Какой инструмент вы использовали для обнаружения дрейфа? |
| 49 | Что такое Evidently AI? Какие отчёты он генерирует? |
| 50 | Как вы оценивали деградацию качества модели? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 51 | Как вы настраивали пороги для обнаружения дрейфа? |
| 52 | Что такое canary deployment? Как вы его реализовали? |
| 53 | Как вы организовали логирование запросов и ответов API? |
| 54 | Как вы визуализировали метрики мониторинга? |
| 55 | Что такое jitter в данных? Как он влияет на прогнозы? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 56 | Как вы идентифицируете причину деградации модели? |
| 57 | Как вы реализовали автоматическое переобучение модели? |
| 58 | Как вы настроили scheduling ретренинга? |
| 59 | Как вы тестировали систему ретренинга на практике? |
| 60 | Как вы интегрировали мониторинг дрейфа в систему алертинга? |

### Блок 5. Задание «Сломай модель» ([ПЗ5](pz05_break_the_model.md))

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 61 | В чём суть задания «Сломай модель»? |
| 62 | Какой сценарий дрейфа вы разработали? |
| 63 | На сколько процентов ухудшились метрики при вашем сценарии? |
| 64 | Как вы подтвердили, что модель действительно «сломалась»? |
| 65 | Как вы предложили восстановить качество модели? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 66 | Как вы создали синтетический сценарий дрейфа в коде? |
| 67 | Как вы оценили влияние дрейфа на разные модели (ARIMA, XGBoost, LSTM)? |
| 68 | Как вы интерпретировали результаты дрейфа для бизнеса? |
| 69 | Как вы интегрировали задание «Сломай модель» в CI/CD-пайплайн? |
| 70 | Какие признаки указывают на необходимость дообучения в реальном времени? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 71 | Как вы оцениваете чувствительность модели к различным типам дрейфа? |
| 72 | Как вы разрабатывали стратегию дообучения для разных типов дрейфа? |
| 73 | Как вы автоматизировали обнаружение дрейфа в пайплайне? |
| 74 | Как проверить, что ретренинг улучшает качество на дрейфовых данных? |
| 75 | Какие меры предприняли для предотвращения переобучения при ретренинге? |

---

## 10. Полезные ресурсы

| Инструмент | Документация |
|------------|--------------|
| Evidently UI Dashboard | [evidentlyai.com/blog/evidently-04-ml-monitoring-dashboard](https://www.evidentlyai.com/blog/evidently-04-ml-monitoring-dashboard) |
| Evidently + Grafana | [evidentlyai.com/blog/evidently-and-grafana-ml-monitoring-live-dashboards](https://www.evidentlyai.com/blog/evidently-and-grafana-ml-monitoring-live-dashboards) |
| Prefect | [docs.prefect.io](https://docs.prefect.io/) |
| MLflow | [mlflow.org/docs/latest](https://mlflow.org/docs/latest/) |

---

## 11. Финал PRC

ЛР10 завершает **MVP**: API ([ЛР9](lab09_containerization_cicd.md)) + мониторинг + retrain pipeline — демо на защите проекта.
