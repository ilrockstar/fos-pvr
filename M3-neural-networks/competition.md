# Соревнование «Forecast Cup: Чья модель точнее?»

**Модуль 3.** Нейросетевые модели и оценка устойчивости  
**Формат:** COP (соревнование в стиле Kaggle)  
**Комpetенции:** ML-3.2 (П), ML-3.1 (С)  
**Время:** 2–3 недели (параллельно ЛР5–ЛР6)  
**Команды:** 2–4 человека

---

## О соревновании

Команды соревнуются в **прогнозе суточной нагрузки электроэнергии** на скрытом периоде. Побеждает модель с **наилучшей метрикой SMAPE** на private leaderboard (оценивает преподаватель).

Соревнование объединяет три темы модуля:

| Тема | Как проявляется в COP |
|------|------------------------|
| Нейросетевые методы | Обязательно: LSTM, GRU, MLP или иная **нейросетевая** архитектура |
| Обучение и валидация | Walk-forward / val split, early stopping — отражено в отчёте |
| Метрики качества | Рanking по **SMAPE**, дополнительно MAE и RMSE в отчёте |

Сквозной ряд курса (`load_daily_clean.csv`) используется как основа; для COP преподаватель выдаёт **обрезанный train** — без финального holdout.

---

## Постановка задачи

**Цель:** для каждой даты из тестового периода предсказать `load_mwh` — суточное потребление, МВт·ч.

| Параметр | Значение |
|----------|----------|
| Горизонт | **1 шаг** (прогноз на следующие сутки, direct forecasting) |
| Частота | 1 наблюдение = 1 сутки |
| Train (public) | Все даты **до** `TRAIN_END` (выдаёт преподаватель) |
| Test (private) | Следующие **H = 60** суток после `TRAIN_END` — **метки скрыты** |
| Baseline | Naive ( $ \hat{y}_t = y_{t-1}$ ) и public score SARIMA/ML — см. [baselines.md](competition/baselines.md) |

---

## Данные

Преподаватель публикует в LMS / репозитории папку `competition/data/`:

```
competition/data/
├── train.csv              # date, load_mwh — только train-период
├── sample_submission.csv  # date, load_mwh (placeholder 0)
└── README_data.md         # TRAIN_END, правила, запреты
```

**Студентам не выдаётся** файл с истинными значениями test-периода.

### Формат `train.csv`

```csv
date,load_mwh
2020-01-01,42150.3
2020-01-02,41802.1
...
```

### Формат submission

```csv
date,load_mwh
2024-03-01,43200.0
2024-03-02,42800.5
...
```

- Столбцы: **`date`** (YYYY-MM-DD), **`load_mwh`** (float, прогноз)
- Ровно **H строк** — все даты из `sample_submission.csv`
- Без пропусков и дубликатов дат

---

## Правила (Rulebook)

### 1. Состав команды

- **2–4** обучающихся;
- одна команда = один аккаунт / один email для сдачи;
- название команды: латиница, без пробелов (например, `loadmasters`).

### 2. Допустимые модели

**Обязательно:** в pipeline есть **обучаемая нейросеть** (Keras/PyTorch):

- LSTM, GRU, Bidirectional LSTM/GRU;
- MLP на flatten-окне последовательности;
- 1D-CNN + Dense, Temporal Fusion — по согласованию.

**Разрешено комбинировать:** нейросеть + лаговые/календарные признаки на входе.

**Запрещено как единственная модель:** только SARIMA / только XGBoost без neural-блока (это baseline модуля 2, не COP).

### 3. Данные и leakage

| Можно | Нельзя |
|-------|--------|
| `train.csv` от организатора | Подглядывать в полный `load_daily_clean` с test-метками |
| Открытые погодные ряды (temp) с **прогнозом temp на test** или historical-only если exog известна ex ante | Exog, недоступная в момент прогноза |
| Признаки из train only (fit scaler на train) | Fit scaler / imputation на test dates с использованием test $y$ |

### 4. Лимит сабмитов

- **5 сабмитов в сутки**, не более **25 за соревнование**;
- учитывается **лучший** private score;
- каждый сабмит — файл + hash ноутбука (commit SHA) в форме.

### 5. Воспроизводимость

Финальный сабмит сопровождается:

- `team_<name>_report.pdf` (2–4 стр.): архитектура, validation, метрики;
- ссылка на репозиторий или `.ipynb` с `random_seed` зафиксирован.

---

## Метрики и leaderboard

### Primary — **SMAPE** (ranking)

$$\text{SMAPE} = \frac{100\%}{n} \sum_{t=1}^{n} \frac{|y_t - \hat{y}_t|}{(|y_t| + |\hat{y}_t|) / 2}$$

Чем **ниже** — тем лучше.

### Secondary (для отчёта)

- **MAE** — интерпретация в МВт·ч;
- **RMSE** — штраф за крупные промахи.

Организатор публикует **public leaderboard** на 20% test (optional mini-holdout) и **private** — финальный ranking на всех 60 днях.

Подробности расчёта — [competition/scoring.md](competition/scoring.md).

---

## Как участвовать (пошагово)

### Шаг 1. Регистрация

Заполнить форму: название команды, ФИО, GitHub (опционально).

### Шаг 2. EDA на train

Используйте навыки [ЛР1–2](../M1-analysis-and-preparation/): сезонность $s=7$, тренд, пропуски.

### Шаг 3. Validation (тема «Обучение и валидация»)

Рекомендуемый протокол — **walk-forward** на последних 90 днях train:

```
[-------- train для fit --------][-- walk-forward val --]
                                 ↑ не подглядывать в test COP
```

Early stopping по val_loss обязателен для нейросети.

### Шаг 4. Обучение neural-модели (тема «Нейросетевые методы»)

Минимальный pipeline:

```python
# фрагмент — реализуйте сами
# X: (n_samples, lookback, n_features), y: (n_samples,)
# model: LSTM / GRU + Dropout + Dense(1)
# scaler.fit только на train portion
```

### Шаг 5. Генерация submission

```python
# predictions.csv — по всем датам sample_submission
# прогноз на каждый день test: используйте известную history + recursive или direct
```

### Шаг 6. Сдача

LMS / email преподавателю:

- `submissions/loadmasters_v3.csv`
- `reports/loadmasters_report.pdf`
- commit SHA

---

## Фрагменты кода

### Метрика SMAPE (локальная проверка на val)

```python
import numpy as np

def smape(y_true, y_pred):
    y_true, y_pred = np.asarray(y_true), np.asarray(y_pred)
    denom = (np.abs(y_true) + np.abs(y_pred)) / 2
    denom = np.where(denom == 0, 1e-8, denom)
    return np.mean(np.abs(y_true - y_pred) / denom) * 100
```

### Окна для LSTM

```python
def make_sequences(values, lookback=14):
    X, y, dates = [], [], []
    for i in range(lookback, len(values)):
        X.append(values[i - lookback:i])
        y.append(values[i])
    return np.array(X), np.array(y)
```

### Recursive forecast на test (1 step rolling)

```python
# history — последние lookback дней известных y из train
# для каждой test-даты: predict one step → append prediction to history
# осторожно: ошибки накапливаются при h>1; здесь h=1 direct по датам sample_submission
```

---

## Baseline «Beat the Boss»

Чтобы пройти **зачётный порог**, команда должна улучшить **naive SMAPE** на private test минимум на **5 % relative** (организатор уточняет точное число после публикации baselines).

Таблица ориентиров — [competition/baselines.md](competition/baselines.md).

---

## Критерии оценки (зачёт + рейтинг)

| Компонент | Вес | Описание |
|-----------|-----|----------|
| Private SMAPE (rank) | 50 % | Место в leaderboard |
| Отчёт: validation + architecture | 25 % | Walk-forward, early stopping, обоснование |
| Воспроизводимость | 15 % | Seed, notebook, метрики сходятся ±1 % |
| Beat naive baseline | 10 % | Порог зачёта |

### Уровни (КРМ)

| Уровень | Критерии |
|---------|----------|
| **Базовый** | Сабмит валиден, neural модель обучена, SMAPE лучше naive |
| **Средний** | Top-50 % leaderboard, отчёт с walk-forward и MAE/RMSE/SMAPE |
| **Продвинутый** | Top-3 или SMAPE в пределах 5 % от лучшей команды, полная воспроизводимость, осмысленный ablation (LSTM vs GRU) |

---

## Календарь (рекомендуемый)

| Дата | Событие |
|------|---------|
| Неделя 9 | Старт: выдача train, регистрация команд |
| Неделя 10 | Public LB открыт (optional), консультация по validation |
| Неделя 11 | Deadline сабмитов, 18:00 |
| Неделя 11 | Защита топ-5: 5 мин / команда — «почему наш подход победил / проиграл» |

---

## FAQ

**Q: Можно ли одному?**  
A: Нет, минимум 2 человека (командный формат COP).

**Q: Можно ли ensemble нейросети + XGBoost?**  
A: Да, если neural block вносит вклад и описан в отчёте.

**Q: Какой lookback выбрать?**  
A: На усмотрение команды; обоснуйте val-метрикой (не подбирайте по test COP).

**Q: Что если SMAPE = inf?**  
A: Проверьте нулевые прогнозы; submission отклоняется при >10 % нулей.

---

## Файлы соревнования

| Файл | Описание |
|------|----------|
| [competition/scoring.md](competition/scoring.md) | Формулы, скript проверки submission |
| [competition/baselines.md](competition/baselines.md) | Naive, seasonal naive, public SARIMA |
| [competition/instructor_guide.md](competition/instructor_guide.md) | Организация для преподавателя |
| [competition/submission_checklist.md](competition/submission_checklist.md) | Чек-лист перед сдачей |

---

## Связь с лабораторными

| COP | ЛР |
|-----|-----|
| Neural pipeline | [ЛР7](lab07_neural_networks.md) |
| Walk-forward, HPO, drift | [ЛР8](lab08_validation_drift.md) |
| MAE, RMSE, SMAPE в отчёте | тема «Метрики качества» |

Лучший pipeline COP можно перенести в `models/best_model/` для модуля 4.
