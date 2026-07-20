# Модель измерения результатов обучения

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**КРМ:** версия 3.0  
**Архитектура:** вариант A — **групповой сквозной проект**, ЛР = milestones

---

## 1. Целевые роли и уровень

| Роль КРМ | Рекомендуемый уровень в дисциплине |
|----------|-------------------------------------|
| Data Analyst | Средний (С) |
| ML Engineer | Средний (С) |
| ML Researcher | Средний (С) |
| MLOps | Средний (С) |

---

## 2. Матрица «Компетенце × оценочное средство»

| Компетенце | ЛР (milestones) | ПЗ / квизы | COP | PRC | Peer | Σ КИМ |
|------------|-----------------|------------|-----|-----|------|-------|
| **BD 1.2** Анализ данных и визуализация | ЛР1 | ПЗ1 | — | ✓ | — | 3 |
| **BD 1.3** Обработка и подготовка данных | ЛР2 | ПЗ2 | — | ✓ | — | 3 |
| **ML-2.2** Интерпретация результатов моделей | ЛР3, ЛР4 | ПЗ3 | — | ✓ | — | 3 |
| **ML-2.3** Визуализация и коммуникация | ЛР2,4,6,8 | — | — | ✓ | ✓ | 3 |
| **ML-3.1** Реализация ML-моделей | ЛР1,3,5,6* | — | ✓ | ✓ | — | 3 |
| **ML-3.2** Настройка и оптимизация | ЛР4–8 | ПЗ3 | ✓ | — | — | 3 |
| **ML-5.1** Анализ устойчивости | ЛР7 | ПЗ4 | ✓ | ✓ | — | 3 |
| **ML-5.2** Управление дрейфом | ЛР7 | ПЗ4, ПЗ5 | ✓ | ✓ | — | 3 |
| **LC-5.1** Проектирование сервисов | ЛР8, П1 | ПЗ5 | — | ✓ | ✓ | 4 |
| **LC-5.2** CI/CD и автоматизация | ЛР8, П2 | — | — | ✓ | — | 3 |

\*ЛР6 — валидация и HPO для всех классов моделей.

**П1–П3** — [практики M4](../M4-infrastructure-and-deployment/README.md) (REST API, Docker, мониторинг).

---

## 3. Детализация по лабораторным (milestones)

| ЛР | Milestone | Компетенце | Индикаторы (дескриптор) | КИМ |
|----|-----------|------------|-------------------------|-----|
| **ЛР1** | EDA | BD 1.2, ML-2.3 | EDA, ACF/PACF, тренд/сезонность, аномалии; визуализация в отчёте команды | [lab01](../M1-analysis-and-preparation/lab01_eda.md) |
| **ЛР2** | Preprocessing | BD 1.3, ML-2.3 | Пропуски, выбросы, STL, ADF/KPSS; `load_daily_clean.csv` | [lab02](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md) |
| **ЛР3** | Statistical models | ML-3.1, ML-3.2, ML-2.2 | ARIMA/SARIMA, ES, AIC/BIC, диагностика остатков | [lab03](../M2-statistical-and-ML-models/lab03_classical_statistical_models.md) |
| **ЛР4** | ML models | ML-3.1, ML-3.2, ML-2.2 | Лаги, календарь, exog, RF/boosting; интерпретация | [lab04](../M2-statistical-and-ML-models/lab04_regression_ml_models.md) |
| **ЛР5** | Neural nets | ML-3.1, ML-2.3 | LSTM/GRU, sequence pipeline | [lab05](../M3-neural-networks/lab05_neural_networks.md) |
| **ЛР6** | Validation / HPO | ML-3.2, ML-2.3 | Walk-forward, early stopping, Optuna | [lab06](../M3-neural-networks/lab06_validation_hyperparameters.md) |
| **ЛР7** | Drift | ML-5.1, ML-5.2, ML-2.3 | Data/concept drift, PSI, retraining plan | [lab07](../M4-infrastructure-and-deployment/lab07_stability_drift.md) |
| **ЛР8** | Deploy | LC-5.1, LC-5.2 | Docker, API, CI/CD, MLflow | [lab08](../M4-infrastructure-and-deployment/lab08_containerization_cicd.md) |

---

## 4. Практические задания (ПЗ)

| ПЗ | Компетенце | Роль | КИМ |
|----|------------|------|-----|
| ПЗ1 Структура ряда | BD 1.2 | Data Analyst | [quiz_pz01](../M1-analysis-and-preparation/quiz_pz01_structure.md) |
| ПЗ2 Стационарность | BD 1.3 | Data Analyst | [quiz_pz02](../M1-analysis-and-preparation/quiz_pz02_stationarity.md) |
| ПЗ3 Метрики | ML-2.2 | ML Engineer / Researcher | [quiz_pz03](../M2-statistical-and-ML-models/quiz_pz03_metrics.md) |
| ПЗ4 Дрейф | ML-5.2 | ML Researcher / MLOps | [quiz_pz04](../M3-neural-networks/quiz_pz04_drift.md) |
| ПЗ5 Сломай модель | ML-5.2 | ML Researcher / MLOps | [pz05](../M4-infrastructure-and-deployment/pz05_break_the_model.md) |

---

## 5. Роли КРМ → индикаторы → модули

### Data Analyst

| Индикатор | Модули / КИМ |
|-----------|--------------|
| EDA, распределения, аномалии | M1 ЛР1, ПЗ1 |
| ACF/PACF, лаги, сезонность | M1 ЛР1, ПЗ1 |
| Пропуски, STL, ADF/KPSS | M1 ЛР2, ПЗ2 |
| Лаговые и календарные признаки | M2 ЛР4 |

### ML Engineer

| Индикатор | Модули / КИМ |
|-----------|--------------|
| ARIMA, SARIMA, ES | M2 ЛР3 |
| RF, boosting, feature engineering | M2 ЛР4 |
| LSTM/GRU | M3 ЛР5 |
| Walk-forward, HPO | M3 ЛР6, COP |
| Метрики MAE/RMSE/SMAPE | ПЗ3, все ЛР |

### ML Researcher

| Индикатор | Модули / КИМ |
|-----------|--------------|
| Сравнение архитектур | ЛР3–6, COP |
| Эксперименты, Optuna | ЛР6 |
| Drift, деградация | ЛР7, ПЗ4, ПЗ5 |
| Выбор модели по метрикам | ПЗ3, PRC |

### MLOps

| Индикатор | Модули / КИМ |
|-----------|--------------|
| REST API | M4 П1, ЛР8 |
| Docker, compose | M4 П2, ЛР8 |
| Логи, metrics, MLflow | M4 П3, ЛР8 |
| Drift monitoring, retraining | ЛР7, ПЗ5, PRC |
| CI/CD | ЛР8 |

---

## 6. Формы контроля × вес (сводка)

| Форма | Элементы | Вес |
|-------|----------|-----|
| Текущая | ПЗ1–ПЗ5 | 10 % |
| Рубежная | Milestones ЛР1–ЛР8 | 40 % |
| Рубежная | COP | 15 % |
| Промежуточная | Peer-review | 5 % |
| Итоговая | PRC (защита MVP) | 30 % |

Подробнее: [grading.md](grading.md).

---

## 7. Шаблон строки для РПД

| № | Модуль | Компетенце | Индикатор | Уровень | Дескриптор | Форма | КИМ |
|---|--------|------------|-----------|---------|------------|-------|-----|
| 1 | M1 | BD 1.2 | 1 | С | Выполняет EDA временного ряда в составе команды, строит ACF/PACF, описывает сезонность | Milestone ЛР1 | [lab01](../M1-analysis-and-preparation/lab01_eda.md) |
| … | … | … | … | … | … | … | … |

*Полная таблица заполняется в [rpd.md](rpd.md) при утверждении программы.*
