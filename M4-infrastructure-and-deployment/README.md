# Модуль 4. Инфраструктура и деплой прогнозных систем

**Формат ФОС:** ЛР9–10 — milestones группового проекта; **ПЗ5** — индивидуально.

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Компетенции КРМ:** LC-5.1 (С), LC-5.2 (С), ML-5.1 (П), ML-5.2 (П)

---

## Состав модуля

| Элемент | Тема | Содержание | Форма контроля |
|---------|------|------------|----------------|
| [ЛР9](lab09_containerization_cicd.md) | Контейнеризация, REST API, CI/CD | Docker, API, CI, MLflow | Milestone, 4 % |
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
ЛР8 (model + drift rules) → ЛР9 (API + Docker + CI + MLflow) → ЛР10 (monitoring + retraining) → ПЗ5 → PRC
```

## Файлы

| Файл | Описание |
|------|----------|
| [lab09_containerization_cicd.md](lab09_containerization_cicd.md) | ЛР9: CI/CD |
| [lab10_monitoring.md](lab10_monitoring.md) | ЛР10: мониторинг |
| [pz05_break_the_model.md](pz05_break_the_model.md) | ПЗ5 |
| [pz05_key.md](pz05_key.md) | Ключ ПЗ5 |
| [instructor_guide.md](instructor_guide.md) | Методические рекомендации |
