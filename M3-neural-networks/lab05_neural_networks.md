# Лабораторная работа 5. Нейросетевые модели (LSTM / GRU)

**Модуль 3.** Нейросетевые модели и оценка устойчивости  
**Время выполнения:** 4 академических часа (+ 4 ч самостоятельной работы)  
**Компетенции:** ML-3.2 (П), ML-3.1 (С)

> **Архитектура A — milestone команды (3–4 чел.):** один репозиторий, общий отчёт. Артефакты: `src/neural_models.py`, `models/lstm/`. Вес: **5 %** — [docs/grading.md](../docs/grading.md).

---

## Краткая теоретическая справка

**Рекуррентные сети (RNN)** обрабатывают последовательность $x_1, \ldots, x_T$, сохраняя скрытое состояние $h_t$. Проблема затухающего градиента ограничивает классические RNN на длинных зависимостях.

**LSTM** (Long Short-Term Memory) использует ворота (input, forget, output) и ячейку памяти для удержания информации на длинных лагах.  
**GRU** (Gated Recurrent Unit) — упрощённая альтернатива с двумя воротами; часто быстрее обучается при сопоставимом качестве.

**Прогнозирование как задача sequence-to-one:** окно прошлых $L$ значений $(y_{t-L}, \ldots, y_{t-1})$ → предсказание $y_t$. Данные представляют тензором `(n_samples, L, n_features)`.

---

## Задача

Постройте **нейросетевую модель прогноза** суточной нагрузки на базе LSTM или GRU. Сравните с лучшими результатами SARIMA (ЛР3) и ML (ЛР4). Подготовьте пайплайн подачи последовательностей для последующей валидации (ЛР6).

**Входные данные:** `load_daily_clean.csv`, опционально признаки из `load_features.csv`.

**Результат:** обученная модель LSTM/GRU, графики обучения, сравнение метрик на test.

---

## Ход работы

### 1. Подготовка последовательностей

1. Нормализуйте целевой ряд (MinMaxScaler или StandardScaler); **fit только на train**.
2. Выберите длину окна $L$ (например, 14 или 30 суток).
3. Сформируйте массивы `X` формы `(N, L, 1)` и `y` формы `(N,)`.
4. Разделите хронологически: train / val / test (например, 70 / 10 / 20 %).

### 2. Архитектура LSTM или GRU

1. Реализуйте модель: 1–2 рекуррентных слоя + Dense(1).
2. Используйте dropout для регуляризации.
3. Loss: MSE или MAE; optimizer: Adam.

### 3. Обучение

1. Обучите модель с `EarlyStopping` по val_loss.
2. Постройте кривые train/val loss.
3. Зафиксируйте число эпох до остановки.

### 4. Оценка и сравнение

1. Денормализуйте прогнозы.
2. Рассчитайте MAE, RMSE, SMAPE на test.
3. Сравните с SARIMA и ML из модулей 2–3 (таблица).
4. Сохраните модель: `models/lstm_v1.keras` (или `.pt` для PyTorch).

### 5. Эксперимент LSTM vs GRU (кратко)

1. Обучите вторую архитектуру (GRU, если основная — LSTM).
2. Сравните метрики и время обучения в 3–5 предложениях.

---

## Фрагменты кода

### Масштабирование и окна

```python
import numpy as np
import pandas as pd
from sklearn.preprocessing import MinMaxScaler

df = pd.read_csv("data/processed/load_daily_clean.csv", parse_dates=["date"])
values = df["load_mwh"].values.reshape(-1, 1)

split = int(len(values) * 0.8)
train_vals = values[:split]

scaler = MinMaxScaler()
scaler.fit(train_vals)
scaled = scaler.transform(values)

def make_sequences(arr, lookback=14):
    X, y = [], []
    for i in range(lookback, len(arr)):
        X.append(arr[i - lookback:i])
        y.append(arr[i])
    return np.array(X), np.array(y)

lookback = 14
X, y = make_sequences(scaled, lookback)
# далее — хронологический split X, y по индексам
```

### LSTM в Keras

```python
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import LSTM, Dense, Dropout

model = Sequential([
    LSTM(64, return_sequences=True, input_shape=(lookback, 1)),
    Dropout(0.2),
    LSTM(32),
    Dropout(0.2),
    Dense(1)
])
model.compile(optimizer="adam", loss="mse", metrics=["mae"])
```

### Early stopping и обучение

```python
from tensorflow.keras.callbacks import EarlyStopping

cb = EarlyStopping(monitor="val_loss", patience=10, restore_best_weights=True)
history = model.fit(
    X_train, y_train,
    validation_data=(X_val, y_val),
    epochs=100, batch_size=32, callbacks=[cb], verbose=1
)
```

### GRU (альтернатива)

```python
from tensorflow.keras.layers import GRU

model_gru = Sequential([
    GRU(64, return_sequences=True, input_shape=(lookback, 1)),
    Dropout(0.2),
    GRU(32),
    Dense(1)
])
```

### Денормализация прогноза

```python
y_pred_scaled = model.predict(X_test)
y_pred = scaler.inverse_transform(y_pred_scaled)
y_true = scaler.inverse_transform(y_test.reshape(-1, 1))
```

### Сохранение

```python
model.save("models/lstm_v1.keras")
import pickle
with open("models/scaler.pkl", "wb") as f:
    pickle.dump(scaler, f)
```

---

## Формат сдачи

- Notebook + отчёт (4–5 страниц): архитектура, кривые обучения, таблица сравнения.
- Модель и scaler в `models/`.
- Краткое сравнение LSTM vs GRU.

---

## Критерии оценки

| Уровень | Критерии |
|---------|----------|
| **Базовый** | Последовательности сформированы, LSTM/GRU обучена, прогноз на test, MAE/RMSE, scaler fit на train |
| **Средний** | Early stopping, val split, сравнение с SARIMA и ML, графики loss, модель сохранена |
| **Продвинутый** | Эксперимент LSTM vs GRU с выводами, обоснованный lookback, мультивариантные входы (лаги + календарь), воспроизводимый пайплайн для ЛР6 |
