# Forecast Cup — scoring и проверка submission

Документ для **организатора** и студентов (локальная валидация).

---

## Формулы метрик

### SMAPE (primary)

```python
import numpy as np
import pandas as pd

def smape(y_true, y_pred):
    y_true = np.asarray(y_true, dtype=float)
    y_pred = np.asarray(y_pred, dtype=float)
    denom = (np.abs(y_true) + np.abs(y_pred)) / 2.0
    denom = np.where(denom < 1e-8, 1e-8, denom)
    return float(np.mean(np.abs(y_true - y_pred) / denom) * 100.0)

def mae(y_true, y_pred):
    return float(np.mean(np.abs(y_true - y_pred)))

def rmse(y_true, y_pred):
    return float(np.sqrt(np.mean((y_true - y_pred) ** 2)))
```

### MAPE (справочно, не для ranking)

Не используется как primary при $y_t \approx 0$ (деление на ноль). При необходимости — только в отчёте с оговоркой.

---

## Валидация формата submission

```python
def validate_submission(sub_path, sample_path):
    sub = pd.read_csv(sub_path, parse_dates=["date"])
    sample = pd.read_csv(sample_path, parse_dates=["date"])

    errors = []
    if list(sub.columns) != ["date", "load_mwh"]:
        errors.append("columns must be: date, load_mwh")
    if len(sub) != len(sample):
        errors.append(f"expected {len(sample)} rows, got {len(sub)}")
    if sub["date"].duplicated().any():
        errors.append("duplicate dates")
    if not sub["date"].equals(sample["date"]):
        errors.append("dates must match sample_submission exactly")
    if sub["load_mwh"].isna().any():
        errors.append("NaN in load_mwh")
    if (sub["load_mwh"] <= 0).mean() > 0.10:
        errors.append(">10% non-positive predictions")

    return errors
```

---

## Подсчёт score (organizer script)

```python
def score_submission(sub_path, solution_path):
    """solution_path — скрытый CSV с ground truth test period"""
    sub = pd.read_csv(sub_path, parse_dates=["date"])
    sol = pd.read_csv(solution_path, parse_dates=["date"])

    merged = sol.merge(sub, on="date", suffixes=("_true", "_pred"))
    if len(merged) != len(sol):
        raise ValueError("merge failed — check dates")

    y_true = merged["load_mwh_true"].values
    y_pred = merged["load_mwh_pred"].values

    return {
        "smape": smape(y_true, y_pred),
        "mae": mae(y_true, y_pred),
        "rmse": rmse(y_true, y_pred),
        "n": len(y_true),
    }
```

---

## Маппинг score → баллы COP (пример)

Пусть $S_i$ — SMAPE команды $i$, $S_{best}$ — лучший SMAPE.

| Место | Баллы (50 % max за rank) |
|-------|--------------------------|
| 1 | 50 |
| 2 | 45 |
| 3 | 40 |
| Top 25 % | 35 |
| Top 50 % | 30 |
| Зачёт (beat naive) | 25 |
| Невалидный / no neural | 0 |

Остальные 50 % — отчёт, воспроизводимость, baseline (см. [competition.md](../competition.md)).

---

## Public leaderboard (optional)

Если test = 60 дней, public = последние 12 дней:

```python
PUBLIC_DAYS = 12
sol_public = sol.tail(PUBLIC_DAYS)
# score only on public dates for feedback
```

Private = все 60 дней — **единственный** критерий финального места.

---

## Тie-break

При равном SMAPE (±0.01):

1. Меньший MAE;
2. Раньше timestamp финального сабмита;
3. Жребий.

---

## Анти-cheat checks

| Проверка | Действие |
|----------|----------|
| Submission идентичен другой команде | Запрос отчёта; при плагиате — 0 |
| Test labels в репозитории | Дисквалификация |
| >25 сабмитов | Игнор лишних |
| Нет neural в финальном отчёте | Max «Базовый», не выше 25 rank-баллов |
