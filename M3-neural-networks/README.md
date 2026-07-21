# Модуль 3. Нейросетевые модели и оценка устойчивости

**Формат ФОС:** ЛР7–8 — milestones группового проекта; COP — параллельно.

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Компетенции КРМ:** ML-3.2 (П), ML-3.1 (С), ML-5.1, ML-5.2

---

## Состав модуля

| ЛР | Тема | Содержание | Форма контроля |
|----|------|------------|----------------|
| [ЛР7](lab07_neural_networks.md) | Нейросетевые модели (LSTM/GRU) | Последовательности, RNN, LSTM, GRU | Milestone, 4 % |
| [ЛР8](lab08_validation_drift.md) | Валидация, HPO, дрейф | Walk-forward, Optuna, PSI | Milestone, 4 % |
| [Forecast Cup](competition.md) | COP | … | 15 % |

> Тема drift — в содержании **ЛР8** и кейсе **ПЗ5**.

---

## Связь с предыдущими работами

- **Данные:** `load_daily_clean.csv` (ЛР2), признаки из ЛР5–6.
- **Baseline:** метрики SARIMA (ЛР3–4) и лучшей ML-модели (ЛР6).
- **Артефакты:** `models/best_model/` из ЛР8 → deploy в M4 (ЛР9–10).

---

## Файлы

| Файл | Описание |
|------|----------|
| [lab07_neural_networks.md](lab07_neural_networks.md) | ЛР7: LSTM / GRU |
| [lab08_validation_drift.md](lab08_validation_drift.md) | ЛР8: validation, HPO, drift |
| [competition.md](competition.md) | Forecast Cup (COP) |
| [competition/](competition/) | Scoring, baselines |
| [instructor_guide.md](instructor_guide.md) | Методические рекомендации |
