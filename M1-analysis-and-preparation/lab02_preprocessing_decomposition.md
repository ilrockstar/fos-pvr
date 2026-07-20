# Лабораторная работа 2. Предобработка и декомпозиция

**Модуль 1.** Анализ и подготовка временных рядов  
**Время выполнения:** 4 академических часа (+ 2 ч самостоятельной работы)  
**Компетенции:** ML-2.2 (С), BD-1.3 (С), ML-2.3 (С)

> **Архитектура A — milestone команды (3–4 чел.):** один репозиторий, общий отчёт. Артефакты: `data/processed/`, `src/preprocessing.py`. Вес: **5 %** — [docs/grading.md](../docs/grading.md).

---

## Краткая теоретическая справка

**Предобработка** приводит ряд к виду, пригодному для моделирования:

- **Пропуски** — интерполяция (линейная, сезонная) или forward fill; выбор зависит от доли и природы пропусков.
- **Выбросы** — замена, winsorization или удаление с документированием.
- **Ресемплинг** — приведение к целевой частоте (здесь: 1 наблюдение = 1 сутки).
- **Нормализация** — MinMax или StandardScaler; параметры fit только на train (важно с ЛР6).

**Декомпозиция STL** (Seasonal-Trend decomposition using Loess) разлагает ряд:  
$y_t = T_t + S_t + R_t$ (тренд + сезонность + остаток). Параметр `period` задаёт длину сезона ($s=7$ для недели, $s=365$ для года).

**Стационарность** — постоянство среднего и дисперсии во времени. Тест **ADF** (H₀: есть единичный корень → нестационарен) и **KPSS** (H₀: стационарен) используются совместно. **Дифференцирование** $\Delta y_t = y_t - y_{t-1}$ или сезонное $\Delta_s y_t = y_t - y_{t-s}$ стабилизирует ряд.

---

## Задача

Продолжая работу над прогнозом нагрузки, **подготовьте очищенный временной ряд** и исследуйте его структурные компоненты. Результат этой работы — единый датасет `load_daily_clean.csv`, который будет использоваться во всех последующих лабораторных.

**Входные данные:** сырой ряд из [ЛР1](lab01_eda.md) (`data/raw/`).

**Результат:** обработанный ряд в `data/processed/`, отчёт с декомпозицией и выводами о стационарности.

---

## Ход работы

### 1. Обработка пропусков и выбросов

1. Загрузите сырой ряд из ЛР1.
2. Заполните пропуски (обоснуйте метод: `interpolate`, `ffill` или сезонная imputation).
3. Обработайте выбросы, выявленные в ЛР1 (замена на NaN + интерполяция или clipping).
4. Зафиксируйте число изменённых точек в отчёте.

### 2. STL-декомпозиция

1. Выберите период сезонности: $s=7$ (неделя) и, при достаточной длине ряда, $s=365$ (год).
2. Постройте STL-декомпозицию для основного $s$.
3. Визуализируйте компоненты: trend, seasonal, residual.
4. Оцените долю дисперсии каждой компоненты.

### 3. Проверка стационарности

1. Проведите ADF- и KPSS-тесты для исходного ряда.
2. При необходимости примените дифференцирование ($d=1$) и/или сезонное ($D=1$, lag=$s$).
3. Повторите тесты для преобразованного ряда.
4. Постройте ACF/PACF стационarized ряда.

### 4. Сохранение артефактов

1. Сохраните `data/processed/load_daily_clean.csv` (столбцы: `date`, `load_mwh`).
2. Сохраните параметры предобработки (JSON/YAML): метод imputation, границы выбросов, $d$, $D$, $s$.
3. Подготовьте краткую «паспортную карточку» ряда для команды моделирования (½ страницы).

---

## Фрагменты кода

### Заполнение пропусков

```python
import pandas as pd

df = pd.read_csv("data/raw/load_daily_raw.csv", parse_dates=["date"], index_col="date")
missing_before = df["load_mwh"].isna().sum()

df["load_mwh"] = df["load_mwh"].interpolate(method="time")  # или другой метод — обоснуйте
print(f"Заполнено пропусков: {missing_before}")
```

### STL-декомпозиция

```python
from statsmodels.tsa.seasonal import STL

series = df["load_mwh"].dropna()
stl = STL(series, period=7, robust=True)  # period=7 — недельная сезонность
result = stl.fit()

result.plot()
# result.trend, result.seasonal, result.resid — отдельные Series
```

### Тесты стационарности

```python
from statsmodels.tsa.stattools import adfuller, kpss

def stationarity_report(s, name="series"):
    adf = adfuller(s.dropna())
    kpss_stat = kpss(s.dropna(), regression="c", nlags="auto")
    print(f"{name}: ADF p={adf[1]:.4f}, KPSS p={kpss_stat[1]:.4f}")

stationarity_report(df["load_mwh"], "original")
diff1 = df["load_mwh"].diff().dropna()
stationarity_report(diff1, "diff d=1")
```

### Сезонное дифференцирование

```python
s = 7
seasonal_diff = df["load_mwh"].diff(s).dropna()
stationarity_report(seasonal_diff, f"seasonal diff s={s}")
```

### Сохранение обработанного ряда

```python
import json

df_clean = df[["load_mwh"]].copy()
df_clean.to_csv("data/processed/load_daily_clean.csv")

meta = {
    "imputation": "time_interpolate",
    "seasonal_period": 7,
    "d": 1,
    "D": 0,
    "outlier_method": "iqr_clip"
}
with open("data/processed/preprocessing_meta.json", "w") as f:
    json.dump(meta, f, indent=2)
```

---

## Формат сдачи

- Notebook/скрипт + отчёт (3–4 страницы).
- Файлы: `load_daily_clean.csv`, `preprocessing_meta.json`.
- Графики декомпозиции и ACF/PACF после преобразований.

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | Пропуски обработаны, STL построена, ряд сохранён в `data/processed/`, проведён ADF-тест |
| **Средний** | ADF + KPSS для исходного и преобразованного ряда, обоснован выбор $d$/$s$, выбросы обработаны документированно |
| **Продвинутый** | Сравнение $s=7$ vs $s=365$, количественная оценка компонент, метаданные предобработки полные и воспроизводимы, паспорт ряда пригоден для передачи в ЛР3 |
