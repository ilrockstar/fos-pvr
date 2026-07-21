# Лабораторная работа 5. Регрессионные и факторные модели

**Модуль 2.** Статистические и ML-модели прогнозирования  
**Ролевой фокус:** ML Engineer  
**Комpetence:** ML-3.1, ML-3.2, ML-2.2 (С)  
**Время:** 1 ч лекция + 1 ч лабораторная (+ 2 ч самостоятельной работы)

> **Milestone команды (3–4 чел.):** `src/r_models.py`, `data/features/`. Вес: **4 %** — [docs/grading.md](../docs/grading.md).

---

## 1. Цель работы

Построить **регрессионные модели** с лаговыми, календарными и exog-признаками; сравнить с финальной SARIMA (ЛР3–4). Подготовить feature table для [ЛР6](lab06_ml_models.md).

---

## 2. Входные требования и пререквизиты

- `load_daily_clean.csv` ([ЛР2](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md)).
- Финальная SARIMA и метрики ([ЛР4](lab04_statistical_diagnostics.md)).
- Опционально: `temp_c` — [data_sources.md](data_sources.md).

**Стек:** pandas, scikit-learn (LinearRegression, Ridge, ElasticNet).

---

## 3. Задание

1. Feature engineering **без data leakage**: лаги, календарь, rolling с `shift(1)`.
2. Linear / Ridge / ElasticNet.
3. Тот же хронологический test, что ЛР3–4.
4. Сравнительная таблица: SARIMA | Linear | Ridge.

**Ожидаемые артефакты:** `src/r_models.py`, `data/features/load_features_regression.csv`, `docs/report/05_regression.md`.

---

## 4. Теоретическая справка

**Регрессионный подход к ВР:** целевая $y_t$ предсказывается по признакам $\mathbf{x}_t$ (лаги, календарь, exog).

| Признак | Пример |
|---------|--------|
| Лаги | $y_{t-1}, y_{t-7}, y_{t-14}$ |
| Календарь | `dow`, `month`, `is_weekend`, sin/cos |
| Rolling | mean/std за 7 дней с **`shift(1)`** |
| Exog | температура, праздники |

> **Data leakage:** rolling и лаги должны использовать только прошлое относительно момента прогноза.

---

## 5. Ход работы

### Этап 1. Feature engineering

1. Лаги $y_{t-1}, y_{t-7}, y_{t-14}$.
2. Календарь: `dow`, `month`, `is_weekend`; sin/cos (опционально).
3. Rolling mean/std с `shift(1)`.
4. Exog: merge по `date` (если доступно).

### Этап 2. Модели

1. Linear Regression, Ridge ($\alpha$ подобрать на val или CV).
2. Метрики: MAE, RMSE, SMAPE.
3. Интерпретация коэффициентов (top-признаки).

### Этап 3. Сравнение

1. Таблица: SARIMA (ЛР4) | Linear | Ridge.
2. Вывод: когда регрессия предпочтительнее ARIMA.

### Фрагменты кода

```python
import pandas as pd
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_absolute_error

df = pd.read_csv("data/processed/load_daily_clean.csv", parse_dates=["date"])
df["lag_1"] = df["load_mwh"].shift(1)
df["lag_7"] = df["load_mwh"].shift(7)
df["dow"] = df["date"].dt.dayofweek
df["roll_mean_7"] = df["load_mwh"].shift(1).rolling(7).mean()
df = df.dropna()

split = int(len(df) * 0.8)
train, test = df.iloc[:split], df.iloc[split:]
features = ["lag_1", "lag_7", "dow", "roll_mean_7"]

model = Ridge(alpha=1.0).fit(train[features], train["load_mwh"])
pred = model.predict(test[features])
print("MAE:", mean_absolute_error(test["load_mwh"], pred))
```

---

## 6. Требования к отчёту

1. Описание признаков и защита от leakage.
2. Таблица метрик vs SARIMA.
3. Интерпретация коэффициентов / важных признаков.
4. Feature table для ЛР6.

---

## 7. Критерии оценки

### 7.1. Детализированная шкала (сумма = 4 % milestone)

| Критерий | Макс. балл |
|----------|------------|
| Feature engineering (лаги, календарь) | 0,8 |
| Отсутствие data leakage | 0,6 |
| Linear/Ridge, метрики | 0,6 |
| Сравнение с SARIMA | 0,4 |
| `r_models.py`, feature table | 0,4 |
| Качество отчёта и защита | 1,2 |
| **Итого** | **4,0** |

### 7.2. Уровни Б / С / П

| Уровень | Критерии |
|---------|----------|
| **Базовый** | Лаги + календарь; Linear/Ridge; метрики; сравнение с SARIMA |
| **Средний** | Rolling без утечки; exog или sin/cos; `r_models.py` |
| **Продвинутый** | Интерпретация коэффициентов; feature table для ЛР6 |

---

## 8. Контрольные вопросы

### Блок 1. Регрессия для временных рядов

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 1 | Какие признаки вы создали для регрессии? |
| 2 | Что такое data leakage в контексте лагов? |
| 3 | Чем Ridge отличается от Linear Regression? |
| 4 | Почему test period должен совпадать с ЛР3–4? |
| 5 | Как регрессия сравнилась с SARIMA? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 6 | Зачем `shift(1)` в rolling-признаках? |
| 7 | Как sin/cos кодируют циклические признаки? |
| 8 | Когда exog-переменные критичны для качества? |
| 9 | Как multicollinearity влияет на лаговые признаки? |
| 10 | Какие признаки передать в ML (ЛР6)? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 11 | Как recursive vs direct multi-step forecast в регрессии? |
| 12 | Когда linear model достаточна вместо boosting? |
| 13 | Как regularization path помогает выбору $\alpha$? |
| 14 | Как calendar effects взаимодействуют с сезонностью SARIMA? |
| 15 | Какие ограничения регрессии мотивируют ЛР6–7? |

---

## 9. Связь с другими элементами ФОС

| Элемент | Связь |
|---------|-------|
| [ЛР4](lab04_statistical_diagnostics.md) | Baseline SARIMA |
| [ЛР6](lab06_ml_models.md) | Feature table → ML |
| [M2/quiz.md](quiz.md) | Формативный квиз (лаги, exog) |
| [Project](../Project/project_guidelines.md) | ML — R; Data — C (features) |
