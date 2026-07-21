# Методические рекомендации: модуль 3

**Формат ФОС:** [ЛР7–8](README.md) — milestones группового проекта; **COP** — параллельно.

## Расписание

| Неделя | ЛР | Фокус |
|--------|-----|-------|
| 9–10 | [ЛР7](lab07_neural_networks.md) | Sequence pipeline → LSTM/GRU → сравнение с baseline |
| 10–11 | [ЛР8](lab08_validation_drift.md) | Walk-forward → HPO (Optuna) → `best_model` → drift rules |
| 9–11 | [Forecast Cup](competition.md) | Параллельно ЛР7–8: сабмиты, leaderboard |

## Forecast Cup (COP)

- Правила: [competition.md](competition.md)
- Организация: [competition/instructor_guide.md](competition/instructor_guide.md)
- Подготовить `train.csv`, `sample_submission.csv`, скрытый `solution.csv`
- Kick-off на неделе 9; deadline — за 1 нед. до защиты топ-5
- Вес COP — **15 %** дисциплины ([grading.md](../docs/grading.md))

## Требования к вычислительным ресурсам

- CPU достаточно для суточных рядов (~1000 точек) и 1–2 LSTM-слоёв.
- GPU ускоряет HPO; при отсутствии — ограничить число trials до 10.

## Связь с модулем 2

Студент должен принести таблицу метрик SARIMA (ЛР3–4) и ML (ЛР6). Без неё сравнение в ЛР7–8 неполное.

## Проверка на защите

**ЛР7:** «Почему scaler fit только на train? Как выбран lookback?»

**ЛР8:** «Чем walk-forward отличается от обычного split? Сколько раз вы «подглядели» в test? Какие артефакты передаёте в ЛР9?»

## Типичные ошибки

| Ошибка | Как выявить |
|--------|-------------|
| Утечка при масштабировании | scaler.fit на всём ряде |
| Многократный подбор по test | test MAE «улучшается» каждую неделю |
| Слишком большой lookback | N_samples ≈ 0 после окон |
| Нет early stopping | val_loss растёт, train падает |

## Оценивание

| Уровень | % |
|---------|---|
| Базовый | 60 |
| Средний | 80 |
| Продвинутый | 100 |

Каждая ЛР7 и ЛР8 — **4 %** дисциплины. COP оценивается отдельно по [competition/scoring.md](competition/scoring.md). См. [grading.md](../docs/grading.md).
