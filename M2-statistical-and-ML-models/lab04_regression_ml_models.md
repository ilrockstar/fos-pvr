# Лабораторная работа 4. Регрессионные и ML-модели

**Модуль 2.** Статистические и ML-модели прогнозирования  
**Время выполнения:** 4 академических часа (+ 2 ч самостоятельной работы)  
**Компетенции:** ML-3.1 (С), ML-3.2 (С), ML-2.3 (С)

> **Архитектура A — milestone команды (3–4 чел.):** один репозиторий, общий отчёт. Артефакты: `src/ml_models.py`, `data/features/`. Вес: **5 %** — [docs/grading.md](../docs/grading.md).

---

## Краткая теоретическая справка

**Регрессионный подход** к прогнозированию формирует таблицу признаков $X_t$ и предсказывает $y_{t+h}$:

- **Лаговые признаки:** $y_{t-1}, y_{t-7}, \ldots$ — автoregressive features.
- **Календарные:** день недели, месяц, праздник, sin/cos-кодирование циклов.
- **Скользящие статистики:** rolling mean, std за окно $w$.
- **Экзогенные переменные:** температура, цена — доступны в момент прогноза или прогнозируются отдельно.

**ML-модели** (Random Forest, Gradient Boosting) улавливают нелинейности и взаимодействия признаков, но требуют больше данных и контроля переобучения.

**Важно:** при формировании признаков нельзя использовать информацию из будущего (data leakage). Train/test split — только хронологический.

---

## Задача

Расширьте прогноз нагрузки, применив **регрессионные и ML-модели** с инженерией признаков. Сравните результаты с SARIMA из [ЛР3](lab03_classical_statistical_models.md) и выберите лучший подход для дальнейшего развития (нейросети в модуле 3).

**Входные данные:** `load_daily_clean.csv`, опционально — `temp_c` (Open-Meteo, см. [data_sources.md](data_sources.md)).

**Результат:** признаковая таблица в `data/features/`, сравнительная таблица метрик, сохранённая ML-модель.

---

## Ход работы

### 1. Feature engineering

1. Создайте лаги: $y_{t-1}, y_{t-7}, y_{t-14}$.
2. Добавьте календарные признаки: `day_of_week`, `month`, `is_weekend`.
3. Добавьте rolling-признаки: mean и std за 7 дней (с `shift(1)!`).
4. Присоедините температуру `temp_c` (если доступна).
5. Удалите строки с NaN после лагов; сохраните `data/features/load_features.csv`.

### 2. Разбиение и baseline

1. Хронологический split (тот же test, что в ЛР3, для сопоставимости).
2. Обучите **Linear Regression** / **Ridge** как простой baseline.

### 3. ML-модели

1. **Random Forest Regressor** — настройте `n_estimators`, `max_depth`.
2. **Gradient Boosting** (sklearn / XGBoost / LightGBM) — базовые гиперпараметры.
3. Для каждой модели рассчитайте MAE, RMSE, SMAPE на test.

### 4. Сравнение с SARIMA

1. Загрузите метрики SARIMA из ЛР3.
2. Заполните сравнительную таблицу (шаблон: [templates/comparison_template.md](templates/comparison_template.md)).
3. Проанализируйте **feature importance** (для RF/boosting).

### 5. Интерпретация и выводы

1. Какие признаки наиболее важны? Согласуется ли это с EDA (ЛР1)?
2. Когда ML выигрывает у SARIMA, когда проигрывает?
3. Сохраните лучшую ML-модель: `models/ml_baseline.pkl`.

---

## Фрагменты кода

### Создание лагов и календаря

```python
import pandas as pd

df = pd.read_csv("data/processed/load_daily_clean.csv", parse_dates=["date"])
df = df.sort_values("date")

for lag in [1, 7, 14]:
    df[f"lag_{lag}"] = df["load_mwh"].shift(lag)

df["dow"] = df["date"].dt.dayofweek
df["month"] = df["date"].dt.month
df["is_weekend"] = (df["dow"] >= 5).astype(int)
```

### Rolling-признаки (без утечки)

```python
df["roll_mean_7"] = df["load_mwh"].shift(1).rolling(7).mean()
df["roll_std_7"] = df["load_mwh"].shift(1).rolling(7).std()
df = df.dropna()
```

### Подключение температуры (Open-Meteo — адаптируйте координаты)

```python
# Пример: daily temperature via Open-Meteo API
# import requests — получите temp_c и merge по date
# df = df.merge(temp_df, on="date", how="left")
```

### Train / test

```python
feature_cols = ["lag_1", "lag_7", "lag_14", "dow", "month", "is_weekend",
                "roll_mean_7", "roll_std_7"]  # + temp_c при наличии
target = "load_mwh"

split_idx = int(len(df) * 0.8)
train_df, test_df = df.iloc[:split_idx], df.iloc[split_idx:]

X_train, y_train = train_df[feature_cols], train_df[target]
X_test, y_test = test_df[feature_cols], test_df[target]
```

### Random Forest и метрики

```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_absolute_error, mean_squared_error
import numpy as np

rf = RandomForestRegressor(n_estimators=100, max_depth=10, random_state=42)
rf.fit(X_train, y_train)
pred = rf.predict(X_test)

mae = mean_absolute_error(y_test, pred)
rmse = np.sqrt(mean_squared_error(y_test, pred))
```

### Feature importance

```python
import pandas as pd

imp = pd.Series(rf.feature_importances_, index=feature_cols).sort_values(ascending=False)
imp.plot(kind="barh", title="Random Forest — feature importance")
```

### Gradient Boosting

```python
from sklearn.ensemble import GradientBoostingRegressor

gb = GradientBoostingRegressor(n_estimators=200, max_depth=4, learning_rate=0.05, random_state=42)
gb.fit(X_train, y_train)
gb_pred = gb.predict(X_test)
```

### Сравнительная таблица

```python
results = {
    "SARIMA": {"MAE": ..., "RMSE": ...},  # из ЛР3
    "Linear": {"MAE": mae_lin, "RMSE": rmse_lin},
    "RF": {"MAE": mae, "RMSE": rmse},
    "GBM": {"MAE": ..., "RMSE": ...},
}
pd.DataFrame(results).T
```

---

## Формат сдачи

- Notebook + отчёт с сравнительной таблицей и графиком прогнозов.
- `data/features/load_features.csv`, `models/ml_baseline.pkl`.
- Вывод: рекомендация модели для модуля 3.

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | Лаги и календарь созданы, Linear + RF обучены, метрики на test, сравнение с SARIMA |
| **Средний** | Rolling-признаки без утечки, boosting добавлен, importance интерпретирован, тот же test что в ЛР3 |
| **Продвинутый** | Экзогенная переменная (температура) или sin/cos-кодирование, осмысленный анализ «SARIMA vs ML», артефакты сохранены для ЛР5–6 |
