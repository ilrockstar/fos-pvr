# Лабораторная работа 8. Валидация, подбор гиперпараметров и анализ дрейфа

**Модуль 3.** Нейросетевые модели и оценка устойчивости  
**Ролевой фокус:** ML Engineer, MLOps  
**Комpetence:** ML-2.2, ML-3.2, ML-5.1, ML-5.2 (С–П)  
**Время:** 2 ч лекция + 2 ч лабораторная (+ 4 ч самостоятельной работы)

> **Milestone команды (3–4 чел.):** `docs/validation.md`, `models/best_model/`, `src/drift_analysis.py`. Вес: **4 %** — [docs/grading.md](../docs/grading.md).

---

## 1. Цель работы

1. Организовать **walk-forward validation** и **HPO (Optuna)** для модели из [ЛР7](lab07_neural_networks.md); зафиксировать `models/best_model/`.
2. Смоделировать **data/concept drift**, рассчитать PSI/скользящий MAE, описать **retraining** для [ЛР9–10](../M4-infrastructure-and-deployment/lab09_containerization_cicd.md).

Объединяет темы «метрики и валидация» + «устойчивость и дрейф» (план ФОС).

---

## 2. Входные требования и пререквизиты

- Обученная модель из [ЛР7](lab07_neural_networks.md) и baselines из ЛР3–6.
- Понимание утечек данных при validation (лекция M3).
- Базовые знания drift (ЛР8 = единственное место квиза «Дрейф» в новом плане ПЗ).

**Стек:** Optuna, numpy/pandas; опционально Evidently (продолжение в ЛР10).

---

## 3. Задание

**Часть A — Validation / HPO**

1. Walk-forward: окно train (напр. 365 дней), horizon 1 шаг.
2. Optuna: `lookback`, `units`, `dropout`, `learning_rate` (10–20 trials).
3. Сохранить `models/best_model/` (`weights`, `scaler`, `hparams.json`, `metrics.json`).

**Часть B — Drift / retraining**

1. Симуляция drift: level shift +10 %; усиление weekend-эффекта.
2. PSI или KS; порог алерта (PSI > 0.2).
3. Retrain → `models/v2_retrained/`; сравнение MAE.
4. Playbook в `docs/validation.md` и `src/drift_analysis.py`.

**Ожидаемые артефакты:** `docs/validation.md`, `models/best_model/`, `src/drift_analysis.py`, `docs/report/08_validation_drift.md`.

---

## 4. Теоретическая справка

| Понятие | Описание |
|---------|----------|
| **Walk-forward** | Скользящее окно: fit → predict → сдвиг; без leakage |
| **HPO (Optuna)** | Байесовская оптимизация гиперпараметров |
| **Data drift** | Изменение распределения входов $P(X)$ |
| **Concept drift** | Изменение $P(Y|X)$ |
| **PSI** | Population Stability Index; PSI > 0.2 — частый порог алерта |

---

## 5. Ход работы

### Часть A. Валидация и HPO

#### Этап 1. Walk-forward

1. Окно обучения, horizon 1 шаг.
2. Цикл: fit → predict → сдвиг.
3. MAE/RMSE/SMAPE на val/test.

#### Этап 2. Optuna

1. Objective: val MAE.
2. Лучшая конфигурация → retrain на train+val.
3. Сохранение `best_model/`.

#### Этап 3. Сравнение

Таблица: SARIMA | ML (ЛР6) | LSTM (ЛР7) | LSTM+HPO.

### Часть B. Дрейф и retraining

#### Этап 4. Baseline «prod»

Метрики best_model на test; median MAE по сегментам.

#### Этап 5. Симуляция drift

- **A:** level shift +10 % с даты $t_0$.
- **B:** усиление weekend-эффекта.
- Прогон без retrain → рост MAE.

#### Этап 6. Детекция и retrain

1. PSI/KS: reference (train) vs окно 30 дней.
2. Retrain на данных после $t_0$.
3. Сравнение MAE до/после.

### Фрагменты кода

```python
import optuna

def objective(trial):
    lookback = trial.suggest_int("lookback", 7, 30)
    units = trial.suggest_int("units", 32, 128)
    dropout = trial.suggest_float("dropout", 0.1, 0.4)
    lr = trial.suggest_float("lr", 1e-4, 1e-2, log=True)
    # train model → return val_mae

study = optuna.create_study(direction="minimize")
study.optimize(objective, n_trials=15)
```

```python
# drift_analysis.py — упрощённый PSI
import numpy as np

def psi(expected, actual, buckets=10):
    exp_pct = np.histogram(expected, bins=buckets)[0] / len(expected)
    act_pct = np.histogram(actual, bins=buckets)[0] / len(actual)
    exp_pct = np.clip(exp_pct, 1e-6, None)
    act_pct = np.clip(act_pct, 1e-6, None)
    return np.sum((act_pct - exp_pct) * np.log(act_pct / exp_pct))
```

---

## 6. Требования к отчёту

1. Протокол walk-forward и split (даты, размеры окон).
2. Таблица Optuna trials (top-5).
3. Winner model и обоснование vs stat/ML.
4. Сценарии drift, графики MAE до/после.
5. Playbook retraining (триггеры, окно, шаги) — для ЛР10.

---

## 7. Критерии оценки

### 7.1. Детализированная шкала (сумма = 4 % milestone)

| Критерий | Макс. балл |
|----------|------------|
| Walk-forward validation | 0,6 |
| Optuna HPO, best_model | 0,8 |
| Сравнение классов моделей | 0,5 |
| Симуляция drift (≥1 сценарий) | 0,6 |
| PSI/KS + retraining v2 | 0,5 |
| `validation.md`, drift_analysis.py | 0,4 |
| Качество отчёта и защита | 1,2 |
| **Итого** | **4,0** |

### 7.2. Уровни Б / С / П

| Уровень | Критерии |
|---------|----------|
| **Базовый** | Walk-forward + best_model; ≥1 сценарий drift; график MAE |
| **Средний** | Optuna; `validation.md`; PSI/KS + retraining v2 |
| **Продвинутый** | Обоснованный winner; playbook retrain; связь с PRC и ЛР10 |

---

## 8. Контрольные вопросы

### Блок 1. Валидация и HPO

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 1 | Чем walk-forward отличается от random k-fold? |
| 2 | Где в вашем pipeline находится test set? |
| 3 | Какие гиперпараметры оптимизировал Optuna? |
| 4 | Как вы выбрали финальную модель (`best_model`)? |
| 5 | Какие метрики сравнивали между классами моделей? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 6 | Как data leakage возникает при неправильном split? |
| 7 | Что такое nested CV и когда она нужна? |
| 8 | Как early stopping соотносится с Optuna? |
| 9 | Когда stat-модель побеждает neural после HPO? |
| 10 | Какие артефакты нужны для deploy (ЛР9)? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 11 | Как multi-objective Optuna (MAE + latency)? |
| 12 | Как backtesting horizon $h>1$ в walk-forward? |
| 13 | Как зафиксировать reproducibility trials (seed, git hash)? |
| 14 | Как model selection bias накапливается при многих trials? |
| 15 | Как best_model регистрируется в MLflow? |

### Блок 2. Дрейф и retraining

**Базовый уровень**

| № | Вопрос |
|---|--------|
| 16 | Что такое data drift и concept drift? |
| 17 | Какие сценарии drift вы симулировали? |
| 18 | Что такое PSI? Какой порог вы использовали? |
| 19 | Как изменился MAE после drift без retrain? |
| 20 | Что включает playbook retraining? |

**Продвинутый уровень**

| № | Вопрос |
|---|--------|
| 21 | Чем KS-test отличается от PSI? |
| 22 | Как скользящий MAE используется для алертов? |
| 23 | Какое окно retrain вы выбрали и почему? |
| 24 | Как concept drift проявился в вашем кейсе нагрузки? |
| 25 | Как drift rules передаются в ЛР10 (Evidently)? |

**Экспертный уровень**

| № | Вопрос |
|---|--------|
| 26 | Как отличить временный spike от устойчивого drift? |
| 27 | Когда retrain вреден (catastrophic forgetting)? |
| 28 | Как champion/challenger тестировать v2 модель? |
| 29 | Как drift detection интегрировать в Airflow DAG? |
| 30 | Как [ПЗ5](../M4-infrastructure-and-deployment/pz05_break_the_model.md) использует ваши сценарии? |

---

## 9. Связь с другими элементами ФОС

| Элемент | Связь |
|---------|-------|
| [ЛР7](lab07_neural_networks.md) | Базовая neural model |
| [ЛР9–10](../M4-infrastructure-and-deployment/lab09_containerization_cicd.md) | Deploy + monitoring |
| [COP](competition.md) | Submission может использовать best_model |
| [ПЗ5](../M4-infrastructure-and-deployment/pz05_break_the_model.md) | Кейс «Сломай модель» |
| [Project](../Project/project_guidelines.md) | ML + MLOps — R за drift |
