# Модуль 4. Инфраструктура и деплой прогнозных систем

**Архитектура A:** ЛР9–10 + практики П1–П3 — milestones группового проекта; **ПЗ5** — индивидуально.

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Компетенции КРМ:** LC-5.1 (С), LC-5.2 (С), ML-5.1 (П), ML-5.2 (П)

> Актуальная тематика: [labs-update.pdf](../labs-update.pdf)

---

## Состав модуля

| Элемент | Тема | Содержание | Форма контроля |
|---------|------|------------|----------------|
| [П1](practice01_rest_api.md) | Проектирование REST API | Контракт, endpoints, валидация, тесты | Практика |
| [П2](practice02_containerization.md) | Контейнеризация и воспроизводимость | Dockerfile, compose, pinning | Практика |
| [П3](practice03_monitoring.md) | Мониторинг прогнозной системы | Логи, metrics, MLflow, playbook | Практика |
| [ЛР9](lab09_containerization_cicd.md) | Контейнеризация, REST API, MLflow | Docker, API, CI, MLflow | Milestone, 4 % |
| [ЛР10](lab10_monitoring.md) | Мониторинг, drift, ретренинг | Evidently, дашборд, Airflow/Prefect | Milestone, 4 % |
| [ПЗ5](pz05_break_the_model.md) | «Сломай модель» | Сценарий деградации модели | 2 %, индив. |

---

## Связь с предыдущими работами

Финальный этап сквозного проекта:

- **Модель:** `models/best_model/` из [ЛР8](../M3-neural-networks/lab08_validation_drift.md).
- **Drift-протокол:** правила из ЛР8 → реализация в ЛР10.
- **Результат семестра:** контейнеризованный сервис с CI/CD и production-ready мониторингом.

---

## Рекомендуемая последовательность

```
ЛР8 (model + drift rules) → П1 (API) → ЛР9 (Docker + CI + MLflow) → П2–П3 → ЛР10 (monitoring) → PRC
```

## Файлы

| Файл | Описание |
|------|----------|
| [practice01_rest_api.md](practice01_rest_api.md) | Практика 1: REST API |
| [practice02_containerization.md](practice02_containerization.md) | Практика 2: Docker |
| [practice03_monitoring.md](practice03_monitoring.md) | Практика 3: мониторинг |
| [practice_rubrics.md](practice_rubrics.md) | Критерии практик |
| [lab09_containerization_cicd.md](lab09_containerization_cicd.md) | ЛР9: CI/CD |
| [lab10_monitoring.md](lab10_monitoring.md) | ЛР10: мониторинг |
| [pz05_break_the_model.md](pz05_break_the_model.md) | ПЗ5 |
| [pz05_key.md](pz05_key.md) | Ключ ПЗ5 |
| [instructor_guide.md](instructor_guide.md) | Методические рекомендации |
