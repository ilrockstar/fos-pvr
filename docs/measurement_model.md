# Модель измерения результатов обучения

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**КРМ:** версия 3.0 ([data/krm-v3.0.xlsx](../data/krm-v3.0.xlsx))  
**Архитектура ФОС:** **групповой сквозной проект**, ЛР = milestones (10 шт.)

> Подписи компетенций и индикаторов — по КРМ 3.0. Сокращения (напр. «ML-2.2») не заменяют официальное название индикатора.

---

## 1. Целевые роли и уровень

| Роль КРМ | Рекомендуемый уровень в дисциплине |
|----------|-------------------------------------|
| Data Analyst | Средний (С) |
| ML Engineer | Средний (С) |
| ML Researcher | Средний (С) |
| MLOps | Средний (С) |

---

## 2. Матрица «Компетенции × оценочное средство»

| Компетенции | ЛР (milestones) | ПЗ / квизы | COP | PRC | Peer | Σ КИМ |
|------------|-----------------|------------|-----|-----|------|-------|
| **BD 1.2** Обоснование методов анализа данных и визуализация | ЛР1 | ПЗ1 | — | ✓ | — | 3 |
| **BD 1.3** Обработка и подготовка данных, EDA | ЛР2 | ПЗ2 | — | ✓ | — | 3 |
| **ML-2.2** Предварительная обработка данных и работа с признаками | ЛР2, ЛР4, ЛР8 | ПЗ3 | — | ✓ | — | 3 |
| **ML-2.3** Кросс-валидация, метрики оценки качества | ЛР2, ЛР4, ЛР6 | — | — | ✓ | ✓ | 3 |
| **ML-3.1** Реализация моделей МО, выбор алгоритмов и параметров | ЛР1, ЛР3, ЛР5, ЛР6, ЛР7 | ПЗ1 | ✓ | ✓ | — | 4 |
| **ML-3.2** Классические методы МО для временных рядов | ЛР4–8 | — | ✓ | — | — | 3 |
| **ML-5.1** Анализ устойчивости моделей, переобучение, дрейф | ЛР8 | — | ✓ | ✓ | — | 3 |
| **ML-5.2** Устойчивость к атакам и искажениям данных | ЛР8 | — | ✓ | ✓ | — | 3 |
| **LC-5.1** Настройка развёртывания и сопровождения моделей МО | ЛР9, ЛР10 | ПЗ4, ПЗ5 | — | ✓ | ✓ | 4 |
| **LC-5.2** Автоматизация, CI/CD, виртуализация и контейнеризация | ЛР9, ЛР10 | ПЗ5 | — | ✓ | — | 3 |

---

## 3. Детализация по лабораторным (milestones)

| ЛР | Milestone | Компетенции | Индикаторы (дескриптор) | КИМ |
|----|-----------|------------|-------------------------|-----|
| **ЛР1** | EDA | BD 1.2, ML-3.1 | EDA, ACF/PACF, тренд/сезонность, аномалии | [lab01](../M1-analysis-and-preparation/lab01_eda.md) |
| **ЛР2** | Preprocessing | BD 1.3, ML-2.2, ML-2.3 | Пропуски, выбросы, STL, ADF/KPSS, преобразования | [lab02](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md) |
| **ЛР3** | Statistical models | ML-3.1, ML-3.2 | Baseline, ES, SARIMA, ACF/PACF | [lab03](../M2-statistical-and-ML-models/lab03_classical_statistical_models.md) |
| **ЛР4** | Stat diagnostics | ML-3.2, ML-2.2, ML-2.3 | AIC/BIC, подбор, диагностика остатков, метрики | [lab04](../M2-statistical-and-ML-models/lab04_statistical_diagnostics.md) |
| **ЛР5** | Regression | ML-3.1, ML-3.2 | Лаги, календарь, exog, сравнение регрессионных подходов | [lab05](../M2-statistical-and-ML-models/lab05_regression_factor_models.md) |
| **ЛР6** | ML models | ML-3.1, ML-3.2, ML-2.3 | RF/XGBoost, сравнение по метрикам | [lab06](../M2-statistical-and-ML-models/lab06_ml_models.md) |
| **ЛР7** | Neural nets | ML-3.1, ML-3.2 | LSTM/GRU, sequence pipeline | [lab07](../M3-neural-networks/lab07_neural_networks.md) |
| **ЛР8** | Validation / drift | ML-2.2, ML-3.2, ML-5.1, ML-5.2 | Walk-forward, Optuna, PSI, retraining | [lab08](../M3-neural-networks/lab08_validation_drift.md) |
| **ЛР9** | Deploy | LC-5.1, LC-5.2 | Docker, API, CI/CD, MLflow | [lab09](../M4-infrastructure-and-deployment/lab09_containerization_cicd.md) |
| **ЛР10** | Monitoring | LC-5.1, LC-5.2 | Inference metrics, drift alerts, prod-сопровождение | [lab10](../M4-infrastructure-and-deployment/lab10_monitoring.md) |

---

## 4. Практические задания (ПЗ)

| ПЗ | Модуль | Компетенции | Роль | КИМ |
|----|--------|------------|------|-----|
| ПЗ1 Остаточные знания ML | M1 | ML-3.1, BD 1.2 | все | [quiz_pz1](../M1-analysis-and-preparation/quiz_pz1_ml_basics.md) |
| ПЗ2 Структура ряда | M1 | BD 1.2 | Data Analyst | [quiz_pz01](../M1-analysis-and-preparation/quiz_pz01_structure.md) |
| ПЗ3 Стационарность | M1 | BD 1.3, ML-2.2 | Data Analyst | [quiz_pz02](../M1-analysis-and-preparation/quiz_pz02_stationarity.md) |
| ПЗ4 Метрики | M2 | LC-5.1, ML-2.3 | ML Engineer / Researcher | [quiz_pz03](../M2-statistical-and-ML-models/quiz_pz03_metrics.md) |
| ПЗ5 Сломай модель | M4 | LC-5.1, LC-5.2 | ML Researcher / MLOps | [pz05](../M4-infrastructure-and-deployment/pz05_break_the_model.md) |

---

## 5. Роли КРМ → индикаторы → модули

### Data Analyst

| Индикатор | Модули / КИМ |
|-----------|--------------|
| EDA, распределения, аномалии | M1 ЛР1, ПЗ1–2 |
| ACF/PACF, лаги, сезонность | M1 ЛР1, ПЗ2 |
| Пропуски, STL, ADF/KPSS | M1 ЛР2, ПЗ3 |
| Лаговые и календарные признаки | M2 ЛР5 (ML-2.2) |

### ML Engineer

| Индикатор | Модули / КИМ |
|-----------|--------------|
| Baseline, SARIMA, ES | M2 ЛР3–4 |
| Регрессия, RF/XGBoost | M2 ЛР5–6 |
| LSTM/GRU, validation | M3 ЛР7–8 |
| COP submission | M3 COP |

### MLOps

| Индикатор | Модули / КИМ |
|-----------|--------------|
| REST API, Docker | M4 ЛР9–10 |
| CI/CD, MLflow | M4 ЛР9 (LC-5.2) |
| Drift monitoring | M3 ЛР8, M4 ЛР10 |
| Playbook retraining | M4 ЛР10, ПЗ5, PRC |

---

## 6. Связанные документы

- [grading.md](grading.md)
- [practical_assignments.md](practical_assignments.md)
- [Project/rubrics.md](../Project/rubrics.md)
