# Лабораторная работа 6. Валидация и подбор гиперпараметров

**Модуль 3.** Нейросетевые модели и оценка устойчивости  
**Время выполнения:** 2 академических часа (+ 4 ч самостоятельной работы)  
**Компетенции:** ML-3.2 (С), ML-3.1 (С)

> **Архитектура A — milestone команды (3–4 чел.):** один репозиторий, общий отчёт. Артефакты: `docs/validation.md`, `models/best_model/`. Вес: **5 %** — [docs/grading.md](../docs/grading.md).

---

## Краткая теоретическая справка

**Hold-out validation** — разбиение на train/val/test. Для временных рядов split **только хронологический**.

**Walk-forward validation** (скользящая проверка): модель обучается на окне прошлого, прогнозирует следующий период, окно сдвигается. Даёт более реалистичную оценку качества в продуктиве.

**Подбор гиперпараметров (HPO):** lookback $L$, число слоёв, размер скрытого состояния, dropout, learning rate. Методы: grid search, random search, Optuna.

**Переобучение:** растущий разрыв train/val loss, деградация на test. Противодействие: dropout, early stopping, регуляризация, упрощение архитектуры.

**Метрики:** MAE, RMSE, MAPE, SMAPE — выбор зависит от масштаба и наличия нулей в ряде.

---

## Задача

Организуйте **надёжную процедуру валидации** для нейросетевой модели из [ЛР5](lab05_neural_networks.md). Проведите подбор гиперпараметров, сравните метрики с baseline-моделями и выберите финальную конфигурацию для производственного прототипа (ЛР7–8).

**Входные данные:** пайплайн из ЛР5, метрики SARIMA/ML из модулей 2.

**Результат:** таблица экспериментов, лучшая конфигурация, walk-forward оценка, `models/best_model/` с метаданными.

---

## Ход работы

### 1. Walk-forward validation

1. Определите окно обучения (напр., 365 дней) и горизонт прогноза (1 сутки).
2. Реализуйте цикл: обучение → прогноз на следующий день → сдвиг окна.
3. Соберите прогнозы на val/test периоде.
4. Рассчитайте средние MAE, RMSE, SMAPE по всем шагам.

### 2. Подбор гиперпараметров

1. Выберите ≥ 4 гиперпараметра: `lookback`, `units`, `dropout`, `learning_rate`.
2. Проведите random search или Optuna (10–20 проб).
3. Зафиксируйте лучшую конфигурацию по val MAE.

### 3. Сравнение метрик

1. Заполните таблицу: SARIMA | ML | LSTM (ЛР5) | LSTM (после HPO).
2. Проанализируйте, какая модель предпочтительна и почему (не только по метрикам).

### 4. Финальная модель

1. Переобучите лучшую конфигурацию на train+val.
2. Оцените на test (один раз!).
3. Сохраните: веса модели, scaler, `hparams.json`, `metrics.json`.

### 5. Отчёт для команды MLOps

1. Опишите протокол валидации (1 страница).
2. Укажите риски переобучения и меры, которые применили.

---

## Фрагменты кода

### Walk-forward цикл (упрощённый)

```python
import numpy as np

def walk_forward_forecast(series, lookback, train_min, model_fn):
    """model_fn(train_window) -> обученная модель, predict последнего шага"""
    preds, actuals = [], []
    for t in range(train_min, len(series)):
        train_window = series[t - train_min:t]
        if len(train_window) < lookback + 1:
            continue
        model = model_fn(train_window, lookback)
        pred = model.predict_next(train_window[-lookback:])
        preds.append(pred)
        actuals.append(series[t])
    return np.array(actuals), np.array(preds)
```

### Random search гиперпараметров

```python
import random

param_space = {
    "lookback": [7, 14, 21, 30],
    "units": [32, 64, 128],
    "dropout": [0.1, 0.2, 0.3],
    "lr": [1e-3, 5e-4, 1e-4],
}

best_mae, best_params = float("inf"), None
for _ in range(15):
    params = {k: random.choice(v) for k, v in param_space.items()}
    mae = train_and_evaluate(params)  # ваша функция — возвращает val MAE
    if mae < best_mae:
        best_mae, best_params = mae, params
```

### Optuna (опционально)

```python
import optuna

def objective(trial):
    lookback = trial.suggest_int("lookback", 7, 30)
    units = trial.suggest_categorical("units", [32, 64, 128])
    dropout = trial.suggest_float("dropout", 0.1, 0.4)
    lr = trial.suggest_loguniform("lr", 1e-4, 1e-2)
    return train_and_evaluate({"lookback": lookback, "units": units,
                               "dropout": dropout, "lr": lr})

study = optuna.create_study(direction="minimize")
study.optimize(objective, n_trials=20)
```

### Метрики MAPE / SMAPE

```python
def smape(y_true, y_pred):
    denom = (np.abs(y_true) + np.abs(y_pred)) / 2
    return np.mean(np.abs(y_true - y_pred) / denom) * 100
```

### Сохранение метаданных

```python
import json

meta = {
    "best_params": best_params,
    "test_mae": float(test_mae),
    "test_rmse": float(test_rmse),
    "validation": "walk_forward",
    "baseline_sarima_mae": ...,  # из ЛР3
}
with open("models/best_model/metrics.json", "w") as f:
    json.dump(meta, f, indent=2)
```

---

## Формат сдачи

- Notebook + отчёт (4 страницы): протокол валидации, таблица HPO, финальные метрики.
- Папка `models/best_model/` с моделью и JSON-метаданными.

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | Train/val/test разделены, ≥5 комбинаций гиперпараметров, метрики на test, таблица сравнения с baseline |
| **Средний** | Walk-forward validation реализована, random/Optuna search, early stopping, метаданные сохранены |
| **Продвинутый** | Анализ компромисса точность/сложность, протокол валидации готов для ЛР7–8, обоснован выбор метрики (MAPE vs SMAPE), все эксперименты воспроизводимы |
