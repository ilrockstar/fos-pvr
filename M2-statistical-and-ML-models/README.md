# Модуль 2. Статистические и ML-модели прогнозирования

**Архитектура A:** ЛР3–6 — milestones **группового проекта**.

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Компетенции КРМ:** ML-3.1, ML-2.2, ML-2.3, ML-3.2

> Актуальная тематика: [labs-update.pdf](../labs-update.pdf)

---

## Состав модуля

| ЛР | Тема | Содержание | Форма контроля |
|----|------|------------|----------------|
| [ЛР3](lab03_classical_statistical_models.md) | Классические статистические модели | Baseline, ES, SARIMA/ARIMA | Milestone, 4 % |
| [ЛР4](lab04_statistical_diagnostics.md) | Подбор параметров и диагностика | AIC/BIC, сетка, остатки | Milestone, 4 % |
| [ЛР5](lab05_regression_factor_models.md) | Регрессионные и факторные модели | Лаги, календарь, exog | Milestone, 4 % |
| [ЛР6](lab06_ml_models.md) | ML-модели (RF, XGBoost) | Табличные признаки, ансамбли | Milestone, 4 % |
| [ПЗ4](quiz_pz03_metrics.md) | Квиз «Метрики качества» | MAE, RMSE, SMAPE | 2 %, индив. |
| [quiz.md](quiz.md) | Регрессия, лаги, exog | … | формативно к ЛР5–6 |

---

## Связь с предыдущими работами

Лабораторные работы модуля 2 используют **очищенный ряд** из [ЛР2](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md) (`data/processed/load_daily_clean.csv`). Выводы EDA и параметры стационарности из модуля 1 должны быть отражены в обосновании выбора моделей.

```
ЛР3 (stat) → ЛР4 (диагностика) → ЛР5 (регрессия) → ЛР6 (ML) → ЛР7 (M3)
```

---

## Файлы

| Файл | Описание |
|------|----------|
| [lab03_classical_statistical_models.md](lab03_classical_statistical_models.md) | ЛР3: SARIMA / ES |
| [lab04_statistical_diagnostics.md](lab04_statistical_diagnostics.md) | ЛР4: подбор и диагностика |
| [lab05_regression_factor_models.md](lab05_regression_factor_models.md) | ЛР5: регрессия |
| [lab06_ml_models.md](lab06_ml_models.md) | ЛР6: RF / XGBoost |
| [quiz_pz03_metrics.md](quiz_pz03_metrics.md) | ПЗ4: метрики |
| [quiz_pz03_key.md](quiz_pz03_key.md) | Ключ ПЗ4 |
| [data_sources.md](data_sources.md) | Каталог данных |
| [rubrics_summary.md](rubrics_summary.md) | Сводная таблица рубрик |
| [instructor_guide.md](instructor_guide.md) | Методические рекомендации |
| [templates/](templates/) | Шаблоны дневника и сравнительной таблицы |
