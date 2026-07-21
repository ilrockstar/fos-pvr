# Лабораторная работа 7. Нейросетевые модели (LSTM / GRU)

**Модуль 3.** Нейросетевые модели и оценка устойчивости  
**Ролевой фокус:** ML Engineer, ML Researcher  
**Компетенции:** ML-3.1, ML-3.2 (С–П)  
**Время:** 2 ч лекция + 2 ч лабораторная (+ 4 ч самостоятельной работы)

> **Milestone команды (3–4 чел.):** `src/neural_models.py`, `models/lstm/`. Вес: **4 %** — [docs/grading.md](../docs/grading.md).

---

## 1. Цель работы

Построить **нейросетевую модель** (LSTM/GRU) для прогноза суточной нагрузки; сравнить с SARIMA (ЛР3–4) и ML (ЛР6); подготовить pipeline для [ЛР8](lab08_validation_drift.md).

---

## 2. Входные требования и пререквизиты

- `load_daily_clean.csv` ([ЛР2](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md)).
- Метрики SARIMA и лучшей ML-модели (ЛР3–6).
- Базовое понимание RNN/LSTM (лекция M3, пререквизит «Нейросетевые модели»).

**Стек:** TensorFlow/Keras или PyTorch; sklearn (scaler).

---

## 3. Задание

1. Подготовить последовательности (lookback $L$); scaler **fit только на train**.
2. Обучить LSTM или GRU с EarlyStopping.
3. Метрики MAE, RMSE, SMAPE на test.
4. Краткое сравнение LSTM vs GRU.

**Ожидаемые артефакты:** `src/neural_models.py`, `models/lstm/`, `docs/report/07_neural.md`.

---

## 4. Теоретическая справка

| Архитектура | Описание |
|-------------|----------|
| **RNN** | Скрытое состояние $h_t$; проблема затухающего градиента |
| **LSTM** | Ворота input/forget/output + ячейка памяти |
| **GRU** | Упрощённая альтернатива; часто быстрее обучается |

**Sequence-to-one:** окно $(y_{t-L}, \ldots, y_{t-1}) \rightarrow y_t$. Тензор `(n_samples, L, n_features)`.

---

## 5. Ход работы

### Этап 1. Подготовка последовательностей

1. MinMaxScaler / StandardScaler — fit на train.
2. Lookback $L$ (14 или 30 суток) — обосновать.
3. Массивы `X`: `(N, L, 1)`, `y`: `(N,)`.
4. Split: train / val / test (70 / 10 / 20 %).

### Этап 2. Архитектура

1. 1–2 рекуррентных слоя + Dense(1).
2. Dropout для регуляризации.
3. Loss: MSE/MAE; optimizer: Adam.

### Этап 3. Обучение

1. EarlyStopping по `val_loss`.
2. Кривые train/val loss.
3. Денормализация прогнозов.

### Этап 4. Сравнение

1. Таблица: SARIMA | ML | LSTM/GRU.
2. Эксперимент LSTM vs GRU (метрики, время).

### Фрагменты кода

```python
import numpy as np
from sklearn.preprocessing import MinMaxScaler
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import LSTM, GRU, Dense, Dropout
from tensorflow.keras.callbacks import EarlyStopping

scaler = MinMaxScaler()
train_scaled = scaler.fit_transform(train_vals)
# make_sequences → X, y; хронологический split

model = Sequential([
    LSTM(64, return_sequences=True, input_shape=(lookback, 1)),
    Dropout(0.2),
    LSTM(32),
    Dense(1),
])
model.compile(optimizer="adam", loss="mse", metrics=["mae"])

cb = EarlyStopping(monitor="val_loss", patience=10, restore_best_weights=True)
model.fit(X_train, y_train, validation_data=(X_val, y_val), epochs=100, callbacks=[cb])

model.save("models/lstm/model.keras")
```

---

## 6. Требования к отчёту

1. Архитектура (слои, units, lookback).
2. Кривые обучения.
3. Таблица сравнения с stat/ML.
4. LSTM vs GRU — выводы.
5. Готовность pipeline к ЛР8 (сохранённые scaler, weights).

---

## 7. Критерии оценки

### 7.1. Детализированная шкала (сумма = 4 % milestone)

| Критерий | Макс. балл |
|----------|------------|
| Последовательности, scaler fit на train | 0,6 |
| LSTM/GRU обучена, EarlyStopping | 0,8 |
| Метрики на test (MAE, RMSE, SMAPE) | 0,6 |
| Сравнение с SARIMA , ML и LSTM  | 0,5 |
| LSTM vs GRU, `neural_models.py` | 0,3 |
| Качество отчёта и защита | 1,2 |
| **Итого** | **4,0** |

### 7.2. Уровни Б / С / П

| Уровень | Критерии |
|---------|----------|
| **Базовый** | Sequences; LSTM/GRU обучена; метрики; scaler fit на train |
| **Средний** | EarlyStopping; val split; сравнение с stat/ML; модель сохранена |
| **Продвинутый** | LSTM vs GRU с выводами; lookback обоснован; pipeline для ЛР8 |

---

## 8. Контрольные вопросы

### Блок 1. RNN / LSTM / GRU

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 1 | Чем LSTM отличается от классической RNN? |
| 2 | Что такое lookback window $L$? Как вы его выбрали? |
| 3 | Почему scaler fit только на train? |
| 4 | Что делает EarlyStopping? |
| 5 | Как нейросеть сравнилась с XGBoost/SARIMA? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 6 | Когда GRU предпочтительнее LSTM? |
| 7 | Как dropout снижает overfitting в RNN? |
| 8 | Что такое teacher forcing (если применимо)? |
| 9 | Как multivariate input (лаги + календарь) добавить в LSTM? |
| 10 | Какие гиперпараметры передаются в Optuna (ЛР8)? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 11 | Как gradient clipping помогает при обучении RNN? |
| 12 | Когда Transformer предпочтительнее LSTM для ВР? |
| 13 | Как sequence length влияет на bias-variance? |
| 14 | Как экспортировать Keras/PyTorch model для MLflow (ЛР9)? |
| 15 | Какие ограничения LSTM мотивируют ensemble с stat/ML? |

---

## 9. Связь с другими элементами ФОС

| Элемент | Связь |
|---------|-------|
| [ЛР6](../M2-statistical-and-ML-models/lab06_ml_models.md) | Tabular baseline |
| [ЛР8](lab08_validation_drift.md) | HPO, best_model |
| [COP](competition.md) | Может использовать neural pipeline |
| [Project](../Project/project_guidelines.md) | ML — R за milestone |
