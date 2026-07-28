# Лабораторная работа 3. Классические статистические модели

**Модуль 2.** Статистические и ML-модели прогнозирования  
**Ролевой фокус:** ML Engineer  
**Компетенции:** ML-3.1, ML-3.2 (С)  
**Время:** 2 ч лекция + 2 ч лабораторная (+ 5 ч самостоятельной работы)

> **Milestone команды (3–4 чел.):** `src/stat_models.py`, `models/sarima/`. Вес: **4 %** — [docs/grading.md](../docs/grading.md).

---

## 1. Цель работы

Построить **классические статистические модели** прогноза суточной нагрузки: baseline (naive, Holt-Winters) и **SARIMA/ARIMA**; заложить основу для подбора параметров в [ЛР4](lab04_statistical_diagnostics.md).

---

## 2. Входные требования и пререквизиты

- [ЛР2](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md): `load_daily_clean.csv`, `preprocessing_meta.json` ($d$, $s$).
- Базовое понимание ARIMA/SARIMA (лекция M2).

**Стек:** statsmodels (SARIMAX, Holt-Winters), pandas, numpy.

---

## 3. Задание

1. Хронологический split train (80 %) / test (20 %).
2. Baseline: naive, Holt-Winters (ES).
3. SARIMA/ARIMA: 2–3 конфигурации; сравнение по AIC.
4. Прогноз на test; метрики MAE, RMSE, SMAPE.

**Ожидаемые артефакты:** `src/stat_models.py`, `models/sarima/`, `docs/report/03_statistical.md`.

---

## 4. Теоретическая справка

| Модель | Описание |
|--------|----------|
| **Naive** | $\hat{y}_t = y_{t-1}$ — простейший baseline |
| **Holt-Winters (ES)** | Экспоненциальное сглаживание с трендом и сезонностью |
| **ARIMA$(p,d,q)$** | AR + интегрирование + MA |
| **SARIMA$(p,d,q)(P,D,Q,s)$** | ARIMA с сезонной компонентой |

**AIC/BIC** — критерии выбора модели (меньше — лучше, с оговоркой о простоте). Подбор и диагностика остатков — в ЛР4.

---

## 5. Ход работы

### Этап 1. Подготовка

1. Загрузите ряд; используйте $d$, $s$ из ЛР2.
2. Train/test split; **без перемешивания**.
3. ACF/PACF на train — для обоснования порядка.

### Этап 2. Baseline

1. Naive и seasonal naive (опционально).
2. Holt-Winters (`seasonal_periods=7` или из метаданных).
3. Метрики на test.

### Этап 3. SARIMA / ARIMA

1. Начальный порядок $(p,d,q)(P,D,Q,s)$ по ACF/PACF и ЛР2.
2. Обучите 2–3 конфигурации (`SARIMAX`).
3. Сравните AIC; выберите кандидата для ЛР4.
4. Сохраните модель: `models/sarima/`.

### Этап 4. Отчёт

- Таблица: naive | ES | SARIMA (AIC, MAE, RMSE, SMAPE).
- График train / test / forecast.

### Фрагменты кода

```python
import pandas as pd
from statsmodels.tsa.statespace.sarimax import SARIMAX
from statsmodels.tsa.holtwinters import ExponentialSmoothing

df = pd.read_csv("data/processed/load_daily_clean.csv", parse_dates=["date"], index_col="date")
series = df["load_mwh"]
split = int(len(series) * 0.8)
train, test = series.iloc[:split], series.iloc[split:]

hw = ExponentialSmoothing(train, trend="add", seasonal="add", seasonal_periods=7).fit()
model = SARIMAX(train, order=(1, 1, 1), seasonal_order=(1, 0, 1, 7)).fit(disp=False)
forecast = model.forecast(len(test))
```

---

## 6. Требования к отчёту

1. Описание split и горизонта прогноза.
2. Таблица сравнения моделей.
3. График прогноза vs факт.
4. Обоснование начального порядка SARIMA (ACF/PACF).
5. Вывод для команды: beat naive?

---

## 7. Критерии оценки

### 7.1. Детализированная шкала (сумма = 4 % milestone)

| Критерий | Макс. балл |
|----------|------------|
| Baseline (naive / ES) | 0,5 |
| SARIMA обучена, прогноз на test | 0,8 |
| Хронологический split, метрики | 0,6 |
| ACF/PACF, обоснование порядка | 0,5 |
| `stat_models.py`, сохранение модели | 0,4 |
| Качество отчёта и защита | 1,2 |
| **Итого** | **4,0** |

### 7.2. Уровни Б / С / П

| Уровень | Критерии |
|---------|----------|
| **Базовый** | SARIMA + naive/ES; split; метрики на test |
| **Средний** | ≥2 конфигурации SARIMA; модель сохранена; отчёт |
| **Продвинутый** | Обоснованный порядок по ACF/PACF; воспроизводимый `stat_models.py` |

---

## 8. Контрольные вопросы

### Блок 1. Статистические модели

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 1 | Что такое ARIMA? Расшифруйте $(p,d,q)$. |
| 2 | Чем SARIMA отличается от ARIMA? |
| 3 | Зачем нужны baseline-модели (naive, ES)? |
| 4 | Почему train/test split для ВР — хронологический? |
| 5 | Какие метрики вы использовали для сравнения? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 6 | Как ACF/PACF помогают выбрать $p$ и $q$? |
| 7 | Что такое AIC? Когда модель с меньшим AIC не лучше? |
| 8 | Как $d$ и $s$ из ЛР2 использованы в SARIMA? |
| 9 | Чем Holt-Winters отличается от SARIMA по допущениям? |
| 10 | Когда ES может обойти SARIMA на коротком горизонте? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 11 | Как exogenous variables добавляются в SARIMAX? |
| 12 | Какие риски переобучения SARIMA на train? |
| 13 | Как интерпретировать сезонные коэффициенты $(P,D,Q,s)$? |
| 14 | Когда стоит перейти к регрессии (ЛР5) вместо SARIMA? |
| 15 | Как результаты ЛР3 передаются в ЛР4? |

---

## 9. Связь с другими элементами ФОС

| Элемент | Связь |
|---------|-------|
| [ЛР2](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md) | Входные данные |
| [ЛР4](lab04_statistical_diagnostics.md) | Подбор параметров и диагностика |
| [ПЗ4](../docs/practical_assignments.md) | Квиз «Метрики качества» |
| [Project](../Project/project_guidelines.md) | ML — R за milestone |
