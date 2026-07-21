# Методические рекомендации: модуль 4

## Расписание

| Неделя | Занятие | Фокус |
|--------|---------|-------|
| 12–13 | [ЛР9](lab09_containerization_cicd.md) | REST API, Docker, CI/CD, MLflow, Airflow DAG |
| 13–14 | [ЛР10](lab10_monitoring.md) | Inference metrics, drift alerts, retraining |
| 15 | [ПЗ5](pz05_break_the_model.md) | Кейс «Сломай модель» (индивидуально) |
| 15–16 | PRC | Финальная защита MVP |

**Smoke-test на паре (преподаватель):** после ЛР9 — `docker compose up` + curl `/health`, `/predict`; после ЛР10 — logs + `/metrics` + drift alert.

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
