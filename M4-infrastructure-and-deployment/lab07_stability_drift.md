# Лабораторная работа 7. Анализ устойчивости и дрейфа

**Модуль 4.** Инфраструктура и деплой прогнозных систем  
**Время выполнения:** 2 академических часа (+ 4 ч самостоятельной работы)  
**Компетенции:** ML-5.1 (П), ML-5.2 (П)

> **Архитектура A — milestone команды (3–4 чел.):** один репозиторий, общий отчёт. Артефакты: `src/drift_analysis.py`, `docs/monitoring.md`. Вес: **5 %** — [docs/grading.md](../docs/grading.md).

---

## Краткая теоретическая справка

**Data drift** — изменение распределения входных данных $P(X)$: сдвиг среднего потребления, новый режим (локдаун, аномальная зима).  
**Concept drift** — изменение связи $P(Y|X)$: те же признаки, но другая зависимость (рост доли кондиционеров, энергоэффiciency).

**Деградация модели** — рост ошибки прогноза в продуктиве при неизменном пайплайне.  
**Мониторинг:** отслеживание MAE/RMSE на скользящем окне, тесты распределений (KS, PSI), алерты при превышении порога.

**Retraining** — переобучение по расписанию или по триггеру (drift detected). Важно сохранять воспроизводимость и версионирование моделей.

---

## Задача

Смоделируйте **сценарии деградации** прогнозной модели нагрузки и разработайте протокол мониторинга. Определите, когда необходимо дообучение, и проведите эксперимент retraining на «сдвинутых» данных.

**Входные данные:** лучшая модель из [ЛР6](../M3-neural-networks/lab06_validation_hyperparameters.md), исторический ряд `load_daily_clean.csv`.

**Результат:** отчёт с симуляцией drift, графиками метрик, правилами алертинга и экспериментом retraining.

---

## Ход работы

### 1. Baseline в «продуктиве»

1. Загрузите финальную модель и воспроизведите test-метрики из ЛР6.
2. Разбейте test на 2–3 временных сегмента; рассчитайте MAE по каждому.
3. Зафиксируйте «нормальный» уровень ошибки (median MAE на сегментах).

### 2. Симуляция data drift

1. Создайте сценарий A: **сдвиг уровня** (+10 % к `load_mwh` начиная с даты $t_0$).
2. Создайте сценарий B: **смена сезонности** (усиление weekend-эффекта).
3. Создайте сценарий C (опционально): **аномальный период** (2 недели экстремальных значений).
4. Прогоните модель без переобучения; постройте график MAE во времени.

### 3. Детекция дрейфа

1. Рассчитайте PSI или KS-statistic между reference-окном (train) и текущим окном (скользящие 30 дней).
2. Задайте порог алерта (например, PSI > 0.2).
3. Отметьте на графике моменты срабатывания алерта.

### 4. Retraining

1. При срабатывании drift (или по расписанию «раз в квартал») дообучите/переобучите модель на данных до $t_0 + window$.
2. Сравните MAE до и после retraining на post-drift периоде.
3. Зафиксируйте версию модели: `models/v2_retrained/`.

### 5. Протокол мониторинга

Оформите документ (1–2 страницы):

- какие метрики отслеживать (MAE, PSI, latency);
- пороги алертов;
- playbook: «если алерт → retrain → A/B → deploy».

---

## Фрагменты кода

### Скользящий MAE

```python
import pandas as pd
import numpy as np

def rolling_mae(y_true, y_pred, window=30):
    errors = np.abs(y_true - y_pred)
    return pd.Series(errors).rolling(window).mean()
```

### Симуляция level shift

```python
df = pd.read_csv("data/processed/load_daily_clean.csv", parse_dates=["date"])
t0 = "2023-06-01"
df_drift = df.copy()
mask = df_drift["date"] >= t0
df_drift.loc[mask, "load_mwh"] *= 1.10  # +10% data drift
```

### PSI (Population Stability Index)

```python
def psi(expected, actual, buckets=10):
    """expected, actual — 1D arrays; разбивка по квантилям expected"""
    breakpoints = np.quantile(expected, np.linspace(0, 1, buckets + 1))
    breakpoints[0], breakpoints[-1] = -np.inf, np.inf
    exp_pct = np.histogram(expected, bins=breakpoints)[0] / len(expected)
    act_pct = np.histogram(actual, bins=breakpoints)[0] / len(actual)
    exp_pct = np.clip(exp_pct, 1e-6, None)
    act_pct = np.clip(act_pct, 1e-6, None)
    return np.sum((act_pct - exp_pct) * np.log(act_pct / exp_pct))
```

### KS-тест

```python
from scipy.stats import ks_2samp

ref = train_series.values
cur = current_window.values
stat, p_value = ks_2samp(ref, cur)
print(f"KS stat={stat:.3f}, p={p_value:.4f}")
```

### Логирование метрик (структура)

```python
import json
from datetime import datetime

log_entry = {
    "timestamp": datetime.utcnow().isoformat(),
    "model_version": "v1",
    "mae_30d": float(mae_30d),
    "psi": float(psi_val),
    "alert": psi_val > 0.2,
}
# append to monitoring/metrics.jsonl
```

### Retraining (концептуально)

```python
# 1. Загрузить данные до текущей даты
# 2. Переобучить модель с best_params из hparams.json
# 3. Сохранить models/v2_retrained/ + обновить metrics.json
# 4. Сравнить MAE v1 vs v2 на hold-out после drift
```

---

## Задание «Сломай модель» (опционально, + к продвинутому уровню)

Предложите **сценарий деградации**, при котором модель теряет > 30 % точности, но drift-детектор срабатывает с задержкой > 14 дней. Опишите, как улучшить мониторинг.

---

## Формат сдачи

- Notebook + отчёт (4–5 страниц): 2+ сценария drift, графики, PSI/KS, retraining.
- `monitoring/playbook.md` — протокол мониторинга.
- Модель v2 (если retraining выполнен).

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | ≥1 сценарий drift, график деградации MAE, описаны data vs concept drift |
| **Средний** | 2 сценария, PSI или KS на скользящем окне, порог алерта, эксперимент retraining |
| **Продвинутый** | Playbook мониторинга, сравнение v1/v2, задание «Сломай модель» или предложение A/B-canary деплоя |
