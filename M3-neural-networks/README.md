# Модуль 3. Нейросетевые модели и оценка устойчивости

**Архитектура A:** ЛР5–6 — milestones группового проекта; COP — параллельно.

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Компетенции КРМ:** ML-3.2 (П), ML-3.1 (С)

---

## Состав модуля

| ЛР | Тема | Содержание | Форма контроля |
|----|------|------------|----------------|
| [ЛР5](lab05_neural_networks.md) | Нейросетевые модели (LSTM/GRU) | Последовательности, RNN, LSTM, GRU | СОР, защита |
| [ЛР6](lab06_validation_hyperparameters.md) | Валидация и подбор гиперпараметров | Walk-forward, train/val/test, early stopping | СОР, защита |
| [Forecast Cup](competition.md) | COP | … | 15 % |
| [ПЗ4](quiz_pz04_drift.md) | Квиз «Дрейф данных» | … | 2 %, индив. |

---

## Связь с предыдущими работами

Модуль 3 продолжает сквозной проект:

- **Данные:** `load_daily_clean.csv` (ЛР2), масштабирование — fit на train из ЛР3/4.
- **Baseline:** метрики SARIMA (ЛР3) и лучшей ML-модели (ЛР4) используются для сравнения.
- **Артефакты:** обученная нейросеть сохраняется в `models/` для ЛР7–8.

---

## Файлы

| Файл | Описание |
|------|----------|
| [lab05_neural_networks.md](lab05_neural_networks.md) | ЛР5: LSTM / GRU |
| [lab06_validation_hyperparameters.md](lab06_validation_hyperparameters.md) | ЛР6: валидация и HPO |
| [competition.md](competition.md) | Forecast Cup (COP, Kaggle-стиль) |
| [competition/](competition/) | Scoring, baselines |
| [quiz_pz04_drift.md](quiz_pz04_drift.md) | ПЗ4 |
| [quiz_pz04_key.md](quiz_pz04_key.md) | Ключ ПЗ4 |
| [instructor_guide.md](instructor_guide.md) | Методические рекомендации |
