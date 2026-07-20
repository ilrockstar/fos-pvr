# Лабораторная работа 3. Классические статистические модели

**Модуль 2.** Статистические и ML-модели прогнозирования  
**Время выполнения:** 4 академических часа (+ 2 ч самостоятельной работы)  
**Компетенции:** ML-3.1 (С), ML-2.2 (С)

> **Архитектура A — milestone команды (3–4 чел.):** один репозиторий, общий отчёт. Артефакты: `src/stat_models.py`, `models/sarima/`. Вес: **5 %** — [docs/grading.md](../docs/grading.md).

---

## Краткая теоретическая справка

**AR(p)** — авторегрессия: $y_t = c + \sum_{i=1}^{p} \phi_i y_{t-i} + \varepsilon_t$.  
**MA(q)** — скользящее среднее: $y_t = \mu + \varepsilon_t + \sum_{j=1}^{q} \theta_j \varepsilon_{t-j}$.  
**ARIMA(p,d,q)** — ARMA на $d$-кратно дифференцированном ряде.  
**SARIMA(p,d,q)(P,D,Q,s)** — добавляет сезонные компоненты с периодом $s$.  
**Экспоненциальное сглаживание (ES)** — Holt-Winters для тренда и сезонности; мало параметров, быстрый baseline.

**Подбор порядка:** ACF/PACF, информационные критерии AIC/BIC.  
**Диагностика:** остатки должны быть «белым шумом» (тест Льюнга–Бокса, ACF остатков).  
**Метрики:** MAE, RMSE, MAPE, SMAPE — для сравнения моделей на отложенной выборке.

---

## Задача

На основе подготовленного ряда суточной нагрузки **постройте и сравните классические статистические модели** прогноза. Выберите лучшую модель по совокупности критериев (метрики + диагностика) и сохраните её для последующих лабораторных.

**Входные данные:** `data/processed/load_daily_clean.csv` (из ЛР2), `preprocessing_meta.json`.

**Результат:** обученная SARIMA/ARIMA + baseline ES, отчёт с диагностикой, сохранённая модель в `models/`.

---

## Ход работы

### 1. Подготовка к моделированию

1. Загрузите очищенный ряд; используйте выводы ЛР1–ЛР2 ($d$, $s$).
2. Разделите данные **хронологически**: train (80 %) / test (20 %); не перемешивайте!
3. Постройте ACF/PACF для train-части.

### 2. Подбор ARIMA / SARIMA

1. Определите начальные $(p,d,q)$ по ACF/PACF и метаданным ЛР2.
2. Переберите 3–5 конфигураций SARIMA (или ARIMA, если сезонность слабая).
3. Сравните модели по AIC/BIC на train.
4. Выберите финальную модель с учётом простоты и диагностики.

### 3. Экспоненциальное сглаживание (baseline)

1. Обучите Holt-Winters (`seasonal_periods=7` или 365).
2. Сравните с SARIMA по MAE/RMSE на test.

### 4. Диагностика остатков

1. Постройте график остатков, их гистограмму и ACF.
2. Проведите тест Льюнга–Бокса.
3. Сделайте вывод об адекватности модели.

### 5. Прогноз и сохранение

1. Постройте прогноз на длину test; визуализируйте train / test / forecast.
2. Рассчитайте MAE, RMSE, MAPE (или SMAPE).
3. Сохраните модель: `models/sarima_baseline.pkl` (или аналог).

---

## Фрагменты кода

### Train / test split

```python
import pandas as pd

df = pd.read_csv("data/processed/load_daily_clean.csv", parse_dates=["date"], index_col="date")
series = df["load_mwh"]

split_idx = int(len(series) * 0.8)
train, test = series.iloc[:split_idx], series.iloc[split_idx:]
```

### ACF / PACF на train

```python
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf

plot_acf(train, lags=60)
plot_pacf(train, lags=60, method="ywm")
```

### Подбор SARIMA (перебор сетки — сократите под свои данные)

```python
import itertools
from statsmodels.tsa.statespace.sarimax import SARIMAX

p = d = q = range(0, 2)
P = D = Q = range(0, 2)
s = 7  # из ЛР2

results = []
for order in itertools.product(p, [1], q):
    for seasonal_order in itertools.product(P, [0], Q, [s]):
        try:
            model = SARIMAX(train, order=order, seasonal_order=seasonal_order,
                            enforce_stationarity=False, enforce_invertibility=False)
            fit = model.fit(disp=False)
            results.append((order, seasonal_order, fit.aic, fit.bic))
        except Exception:
            pass

results.sort(key=lambda x: x[2])
best_order, best_seasonal, aic, bic = results[0][0], results[0][1], results[0][2], results[0][3]
```

### Holt-Winters

```python
from statsmodels.tsa.holtwinters import ExponentialSmoothing

hw = ExponentialSmoothing(train, trend="add", seasonal="add", seasonal_periods=7)
hw_fit = hw.fit()
hw_forecast = hw_fit.forecast(len(test))
```

### Диагностика остатков

```python
from statsmodels.stats.diagnostic import acorr_ljungbox

final_model = SARIMAX(train, order=best_order, seasonal_order=best_seasonal).fit(disp=False)
residuals = final_model.resid

lb = acorr_ljungbox(residuals, lags=[10], return_df=True)
print(lb)  # p-value > 0.05 → нет автокорреляции остатков
```

### Метрики

```python
import numpy as np

def mae(y_true, y_pred):
    return np.mean(np.abs(y_true - y_pred))

forecast = final_model.forecast(len(test))
print(f"MAE={mae(test, forecast):.2f}, RMSE={np.sqrt(np.mean((test-forecast)**2)):.2f}")
```

### Сохранение модели

```python
import pickle

with open("models/sarima_baseline.pkl", "wb") as f:
    pickle.dump(final_model, f)
```

---

## Формат сдачи

- Notebook + отчёт (4–5 страниц): таблица сравнения моделей, графики, диагностика.
- Файл модели в `models/`.
- Краткий вывод: «почему выбрана именно эта SARIMA».

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | ARIMA/SARIMA обучена, прогноз на test построен, MAE/RMSE рассчитаны, хронологический split |
| **Средний** | Сравнены ≥3 конфигурации по AIC, добавлен ES-baseline, проведена диагностика остатков (график + Ljung-Box) |
| **Продвинутый** | Обоснован компромисс AIC/BIC vs простота, интерпретация $(p,d,q)(P,D,Q,s)$ с опорой на ЛР1–2, модель сохранена и воспроизводима |
