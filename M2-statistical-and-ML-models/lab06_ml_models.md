# Лабораторная работа 6. ML-модели для временных рядов

**Модуль 2.** Статистические и ML-модели прогнозирования  
**Ролевой фокус:** ML Engineer  
**Компетенции:** ML-3.1, ML-3.2, ML-2.3 (С)  
**Время:** 2 ч лекция + 2 ч лабораторная (+ 2 ч самостоятельной работы)

> **Milestone команды (3–4 чел.):** `src/ml_models.py`, `models/ml_baseline.pkl`. Вес: **4 %** — [docs/grading.md](../docs/grading.md).

---

## 1. Цель работы

Обучить **ансамблевые ML-модели** (RF, XGBoost/LightGBM) на табличных признаках; сравнить со SARIMA и регрессией; выбрать лучший табличный baseline для [ЛР7](../M3-neural-networks/lab07_neural_networks.md).

---

## 2. Входные требования и пререквизиты

- Признаки из [ЛР5](lab05_regression_factor_models.md): `data/features/`.
- Единый test period (ЛР3–5).
- Базовые метрики SARIMA и Ridge для сравнения.

**Стек:** scikit-learn, XGBoost и/или LightGBM.

---

## 3. Задание

1. **Random Forest** и **Gradient Boosting** (XGBoost / LightGBM).
2. Метрики MAE, RMSE, SMAPE на том же test.
3. Feature importance (top-10).
4. Сохранить лучшую модель: `models/ml_baseline.pkl`.

**Ожидаемые артефакты:** `src/ml_models.py`, сравнительная таблица ([templates/comparison_template.md](templates/comparison_template.md)), `docs/report/06_ml.md`.

---

## 4. Теоретическая справка

| Модель | Особенности |
|--------|-------------|
| **Random Forest** | Bagging деревьев; устойчив к шуму; importance «из коробки» |
| **XGBoost / LightGBM** | Boosting; часто лучше на табличных признаках; нужен контроль overfitting |

**Сравнение классов моделей:** stat (ЛР3–4) → regression (ЛР5) → ML (ЛР6) → neural (ЛР7). Все на **одном** test window.

---

## 5. Ход работы

### Этап 1. Данные

1. `data/features/load_features.csv` (из ЛР5 или расширенный).
2. Тот же split, что ЛР3–5.

### Этап 2. Модели

1. RandomForestRegressor (`n_estimators`, `max_depth`).
2. XGBRegressor / LGBMRegressor (`learning_rate`, `max_depth`).
3. Метрики на test.

### Этап 3. Анализ

1. Feature importance (top-10).
2. Таблица: SARIMA | Ridge | RF | XGBoost.
3. Выбор winner для нейросетей (ЛР7).

### Этап 4. Опционально (уровень П)

Сравнение с **mlforecast** / **statsforecast** (Nixtla) — 1 абзац в отчёте.

### Фрагменты кода

```python
from sklearn.ensemble import RandomForestRegressor
from xgboost import XGBRegressor
from sklearn.metrics import mean_absolute_error

rf = RandomForestRegressor(n_estimators=100, max_depth=10, random_state=42)
xgb = XGBRegressor(n_estimators=200, max_depth=4, learning_rate=0.05, random_state=42)

rf.fit(X_train, y_train)
xgb.fit(X_train, y_train)

for name, model in [("RF", rf), ("XGB", xgb)]:
    pred = model.predict(X_test)
    print(name, "MAE:", mean_absolute_error(y_test, pred))
```

---

## 6. Требования к отчёту

1. Гиперпараметры моделей.
2. Сводная таблица vs stat/regression.
3. Feature importance с интерпретацией.
4. Обоснование выбора winner для ЛР7.

---

## 7. Критерии оценки

### 7.1. Детализированная шкала (сумма = 4 % milestone)

| Критерий | Макс. балл |
|----------|------------|
| RF + boosting обучены | 0,7 |
| Единый test, метрики | 0,6 |
| Feature importance | 0,5 |
| Сравнение с SARIMA / Ridge | 0,6 |
| `ml_models.py`, сохранение модели | 0,4 |
| Качество отчёта и защита | 1,2 |
| **Итого** | **4,0** |

### 7.2. Уровни Б / С / П

| Уровень | Критерии |
|---------|----------|
| **Базовый** | RF + один boosting; метрики; сравнение с SARIMA |
| **Средний** | Importance; единый test; `ml_models.py` |
| **Продвинутый** | ≥3 ML-модели; осмысленный выбор winner для ЛР7 |

---

## 8. Контрольные вопросы

### Блок 1. ML для временных рядов

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 1 | Почему RF/XGBoost применяют к табличным признакам, а не к «сырому» ряду? |
| 2 | Какие гиперпараметры вы настраивали? |
| 3 | Как feature importance интерпретировать для lag-признаков? |
| 4 | **RF/XGBoost или SARIMA** — кто точнее на **test**? Назовите **MAE/RMSE** (или SMAPE) и краткий вывод. |
| 5 | Зачем сохранять `ml_baseline.pkl`? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 6 | Чем boosting отличается от bagging (RF)? |
| 7 | Как overfitting проявляется в XGBoost на ВР? |
| 8 | Нужна ли нормализация признаков для tree-based моделей? |
| 9 | Как time-based CV отличается от k-fold? |
| 10 | Когда ML не beat stat baseline — ваши гипотезы? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 11 | Как mlforecast формулирует прогноз multi-step? |
| 12 | Как SHAP дополняет feature importance? |
| 13 | Как class imbalance / редкие события влияют на trees? |
| 14 | Когда переходить к LSTM (ЛР7) вместо XGBoost? |
| 15 | Как ML-модель попадает в validation pipeline ЛР8? |

---

## 9. Связь с другими элементами ФОС

| Элемент | Связь |
|---------|-------|
| [ЛР5](lab05_regression_factor_models.md) | Входные признаки |
| [ЛР7](../M3-neural-networks/lab07_neural_networks.md) | Сравнение tabular vs neural |
| [COP](../M3-neural-networks/competition.md) | Может использовать тот же feature pipeline |
| [Project](../Project/project_guidelines.md) | ML — R за milestone |
