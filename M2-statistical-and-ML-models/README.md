# Модуль 2. Статистические и ML-модели прогнозирования

**Архитектура A:** ЛР3–4 — milestones **группового проекта**.

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Компетенции КРМ:** ML-3.1, ML-2.2, ML-2.3, ML-3.2

---

## Состав модуля

| ЛР | Тема | Содержание | Форма контроля |
|----|------|------------|----------------|
| [ЛР3](lab03_classical_statistical_models.md) | Классические статистические модели | ARIMA, SARIMA, ES, диагностика остатков | Отчёт |
| [ЛР4](lab04_regression_ml_models.md) | Регрессионные и ML-модели | Лаги, календарь, экзогенные переменные, RF, boosting | Отчёт, квиз |
| [ПЗ3](quiz_pz03_metrics.md) | Метрики качества | MAE, RMSE, SMAPE | 2 %, индив. |
| [quiz.md](quiz.md) | Регрессия, лаги, exog | … | формативно к ЛР4 |

---

## Связь с предыдущими работами

Лабораторные работы модуля 2 используют **очищенный ряд** из [ЛР2](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md) (`data/processed/load_daily_clean.csv`). Выводы EDA и параметры стационарности из модуля 1 должны быть отражены в обосновании выбора моделей.

---

## Файлы

| Файл | Описание |
|------|----------|
| [lab03_classical_statistical_models.md](lab03_classical_statistical_models.md) | ЛР3: ARIMA / SARIMA |
| [lab04_regression_ml_models.md](lab04_regression_ml_models.md) | ЛР4: регрессия и ML |
| [quiz_pz03_metrics.md](quiz_pz03_metrics.md) | ПЗ3: метрики |
| [quiz_pz03_key.md](quiz_pz03_key.md) | Ключ ПЗ3 |
| [data_sources.md](data_sources.md) | Каталог данных (в т.ч. экзогенные переменные) |
| [rubrics_summary.md](rubrics_summary.md) | Сводная таблица рубрик |
| [instructor_guide.md](instructor_guide.md) | Методические рекомендации |
| [templates/](templates/) | Шаблоны дневника и сравнительной таблицы |
