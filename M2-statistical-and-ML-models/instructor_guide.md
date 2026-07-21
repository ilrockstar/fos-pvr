# Методические рекомендации: модуль 2

**Формат ФОС:** [ЛР3–6](README.md) — milestones **группового проекта**.

## Расписание

| Неделя | ЛР / ПЗ | Фокус на паре |
|--------|---------|---------------|
| 5–6 | [ЛР3](lab03_classical_statistical_models.md) | Baseline → Holt-Winters → SARIMA/ARIMA |
| 6–7 | [ЛР4](lab04_statistical_diagnostics.md) | AIC/BIC, сетка порядков → диагностика остатков |
| 7 | [ПЗ4](quiz_pz03_metrics.md) | 15 мин (LMS), метрики MAE/RMSE/SMAPE |
| 7–8 | [ЛР5](lab05_regression_factor_models.md) | Лаговые и календарные признаки, exog → сравнение с SARIMA |
| 8–9 | [ЛР6](lab06_ml_models.md) | RF / XGBoost → сравнение с ЛР3–5 |

Ключ ПЗ4: [quiz_pz03_key.md](quiz_pz03_key.md). График ПЗ: [practical_assignments.md](../docs/practical_assignments.md).

## Связь с модулем 1

Перед ЛР3 проверьте наличие у студентов:

- `load_daily_clean.csv` из ЛР2;
- описание сезонности и стационарности в отчёте;
- согласованный train/test split (хронологический).

## Типичные ошибки

| Ошибка | Где | Вопрос на защите |
|--------|-----|------------------|
| Утечка через `shift(0)` | ЛР5–6 | «Откуда в строке $t$ берётся $y_t$?» |
| Случайный split | ЛР5–6 | «Почему не shuffle?» |
| SARIMA на нестационарном ряде | ЛР3–4 | «Какие diff применили?» |
| Подбор SARIMA только по train MAE | ЛР4 | «Почему AIC/BIC, а не in-sample RMSE?» |
| Сравнение моделей на разных test | ЛР3–6 | «Один и тот же hold-out?» |

## Оценивание

Каждая ЛР3–6 — **4 %** дисциплины. См. [grading.md](../docs/grading.md).
