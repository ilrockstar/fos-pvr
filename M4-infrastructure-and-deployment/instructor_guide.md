# Методические рекомендации: модуль 4

## Расписание

| Неделя | Занятие | Фокус |
|--------|---------|-------|
| 12–13 | [ЛР9](lab09_containerization_cicd.md) | Docker, CI/CD, MLflow, Airflow DAG |
| 13–14 | [ЛР10](lab10_monitoring.md) | Inference metrics, drift alerts |
| 15 | [П1](practice01_rest_api.md) | Контракт API → `/health`, `/predict`, pytest |
| 15 | [П2](practice02_containerization.md) | Dockerfile → compose → reproducibility |
| 16 | [П3](practice03_monitoring.md) | JSON logs → `/metrics` → MLflow → playbook |

**На одной паре (2 ч):** П1 + начало П2. **На второй:** завершение П2 + П3 (или репетиция demo перед PRC).

## Практические занятия

| Практика | Время на паре | Дома | Критерии |
|----------|---------------|------|----------|
| П1 | 60 мин | тесты, OpenAPI | [practice01](practice01_rest_api.md) |
| П2 | 60 мин | reproducibility checklist | [practice02](practice02_containerization.md) |
| П3 | 90–120 мин | MLflow, симуляция алерта | [practice03](practice03_monitoring.md) |

Сводная рубрика: [practice_rubrics.md](practice_rubrics.md).

**Smoke-test на паре (преподаватель):** после ЛР9 — `docker compose up` + curl; после ЛР10 — logs + `/metrics` + drift alert.

## Предварительные требования

Студент должен иметь:

- `models/best_model/` из [ЛР8](../M3-neural-networks/lab08_validation_drift.md);
- правила drift / retraining из ЛР8.

При отсутствии — выдать эталонную модель, но без уровня «Продвинутый».

## Защита ЛР9–10

Формат: 10 мин demo + 5 мин вопросы.

1. `docker compose up` — сервис healthy.
2. `curl /predict` → прогноз; `curl /metrics` → counters.
3. JSON-лог последнего запроса (ЛР10).
4. Green CI run (GitHub Actions).
5. Playbook: «MAE +30%» — первые 3 шага (связь ЛР8 + ЛР10).

## Типичные ошибки

| Ошибка | Решение |
|--------|---------|
| Модель не монтируется в контейнер | COPY models/ в Dockerfile |
| Разные версии sklearn/tf локально и в Docker | pin versions в requirements.txt |
| CI падает на docker build | добавить шаг только на main или использовать buildkit cache |
| API без валидации | 400 при короткой history |

## Связь с PRC

ЛР10 и **ПЗ5** могут быть связаны с проектным кейсом (PRC): студенты предлагают сценарий «Сломай модель» для чужого сервиса.

## Оценивание

Каждая ЛР — **4 %** дисциплины. Уровни Б/С/П — см. [grading.md](../docs/grading.md).

| Уровень | % |
|---------|---|
| Базовый | 60 |
| Средний | 80 |
| Продвинутый | 100 |
