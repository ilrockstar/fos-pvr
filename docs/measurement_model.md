# Модель измерения результатов обучения

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**КРМ:** версия 3.0  
**Архитектура ФОС:** **групповой сквозной проект**, ЛР = milestones (10 шт.)

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
| **BD 1.2** Анализ данных и визуализация | ЛР1 | ПЗ2 | — | ✓ | — | 3 |
| **BD 1.3** Обработка и подготовка данных | ЛР2 | ПЗ3 | — | ✓ | — | 3 |
| **ML-2.2** Интерпретация результатов моделей | ЛР3–6 | ПЗ4 | — | ✓ | — | 3 |
| **ML-2.3** Визуализация и коммуникация | ЛР2,5,8,10 | — | — | ✓ | ✓ | 3 |
| **ML-3.1** Реализация ML-моделей | ЛР1,3,5,7* | ПЗ1 | ✓ | ✓ | — | 3 |
| **ML-3.2** Настройка и оптимизация | ЛР4–8 | ПЗ4 | ✓ | — | — | 3 |
| **ML-5.1** Анализ устойчивости | ЛР8 | ПЗ5 | ✓ | ✓ | — | 3 |
| **ML-5.2** Управление дрейфом | ЛР8, ЛР10 | ПЗ5 | ✓ | ✓ | — | 3 |
| **LC-5.1** Проектирование сервисов | ЛР9, ЛР10 | ПЗ5 | — | ✓ | ✓ | 4 |
| **LC-5.2** CI/CD и автоматизация | ЛР9, ЛР10 | — | — | ✓ | — | 3 |

\*ЛР7 — нейросетевые модели; ЛР8 — валидация и HPO для всех классов моделей.

---

## 3. Детализация по лабораторным (milestones)

| ЛР | Milestone | Компетенции | Индикаторы (дескриптор) | КИМ |
|----|-----------|------------|-------------------------|-----|
| **ЛР1** | EDA | BD 1.2, ML-2.3 | EDA, ACF/PACF, тренд/сезонность, аномалии | [lab01](../M1-analysis-and-preparation/lab01_eda.md) |
| **ЛР2** | Preprocessing | BD 1.3, ML-2.3 | Пропуски, выбросы, STL, ADF/KPSS | [lab02](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md) |
| **ЛР3** | Statistical models | ML-3.1, ML-3.2 | Baseline, ES, SARIMA, ACF/PACF | [lab03](../M2-statistical-and-ML-models/lab03_classical_statistical_models.md) |
| **ЛР4** | Stat diagnostics | ML-3.2, ML-2.2 | AIC/BIC, подбор, диагностика остатков | [lab04](../M2-statistical-and-ML-models/lab04_statistical_diagnostics.md) |
| **ЛР5** | Regression | ML-3.1, ML-2.2 | Лаги, календарь, exog | [lab05](../M2-statistical-and-ML-models/lab05_regression_factor_models.md) |
| **ЛР6** | ML models | ML-3.1, ML-3.2 | RF/XGBoost, сравнение с stat | [lab06](../M2-statistical-and-ML-models/lab06_ml_models.md) |
| **ЛР7** | Neural nets | ML-3.1, ML-2.3 | LSTM/GRU, sequence pipeline | [lab07](../M3-neural-networks/lab07_neural_networks.md) |
| **ЛР8** | Validation / drift | ML-3.2, ML-5.1, ML-5.2 | Walk-forward, Optuna, PSI, retraining | [lab08](../M3-neural-networks/lab08_validation_drift.md) |
| **ЛР9** | Deploy | LC-5.1, LC-5.2 | Docker, API, CI/CD, MLflow | [lab09](../M4-infrastructure-and-deployment/lab09_containerization_cicd.md) |
| **ЛР10** | Monitoring | ML-5.2, LC-5.1 | Inference metrics, drift alerts | [lab10](../M4-infrastructure-and-deployment/lab10_monitoring.md) |

---

## 4. Практические задания (ПЗ)

| ПЗ | Модуль | Компетенции | Роль | КИМ |
|----|--------|------------|------|-----|
| ПЗ1 Остаточные знания ML | M1 | ML-3.1, BD 1.2 | все | [quiz_pz1](../M1-analysis-and-preparation/quiz_pz1_ml_basics.md) |
| ПЗ2 Структура ряда | M1 | BD 1.2 | Data Analyst | [quiz_pz01](../M1-analysis-and-preparation/quiz_pz01_structure.md) |
| ПЗ3 Стационарность | M1 | BD 1.3 | Data Analyst | [quiz_pz02](../M1-analysis-and-preparation/quiz_pz02_stationarity.md) |
| ПЗ4 Метрики | M2 | ML-2.2 | ML Engineer / Researcher | [quiz_pz03](../M2-statistical-and-ML-models/quiz_pz03_metrics.md) |
| ПЗ5 Сломай модель | M4 | ML-5.2, LC-5.1, LC-5.2 | ML Researcher / MLOps | [pz05](../M4-infrastructure-and-deployment/pz05_break_the_model.md) |

---

## 5. Роли КРМ → индикаторы → модули

### Data Analyst

| Индикатор | Модули / КИМ |
|-----------|--------------|
| EDA, распределения, аномалии | M1 ЛР1, ПЗ2 |
| ACF/PACF, лаги, сезонность | M1 ЛР1, ПЗ2 |
| Пропуски, STL, ADF/KPSS | M1 ЛР2, ПЗ3 |
| Лаговые и календарные признаки | M2 ЛР5 |

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
| CI/CD, MLflow | M4 ЛР9 |
| Drift monitoring | M3 ЛР8, M4 ЛР10 |
| Playbook retraining | M4 ЛР10, PRC |

---

## 6. Связанные документы

- [grading.md](grading.md)
- [practical_assignments.md](practical_assignments.md)
- [Project/rubrics.md](../Project/rubrics.md)
