# Модуль 4. Инфраструктура и деплой прогнозных систем

**Архитектура A:** ЛР7–8 + практики П1–П3 — milestones группового проекта; **ПЗ5** — индивидуально.

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Компетенции КРМ:** LC-5.1 (С), LC-5.2 (С), ML-5.1 (П), ML-5.2 (П)

---

## Состав модуля

| Элемент | Тема | Содержание | Форма контроля |
|---------|------|------------|----------------|
| [П1](practice01_rest_api.md) | Проектирование REST API | Контракт, endpoints, валидация, тесты | Практика |
| [П2](practice02_containerization.md) | Контейнеризация и воспроизводимость | Dockerfile, compose, pinning | Практика |
| [П3](practice03_monitoring.md) | Мониторинг прогнозной системы | Логи, metrics, MLflow, playbook | Практика |
| [ЛР7](lab07_stability_drift.md) | Анализ устойчивости и дрейфа | Data drift, concept drift, retraining | Milestone |
| [ЛР8](lab08_containerization_cicd.md) | Контейнеризация и CI/CD | Docker, API, GitHub Actions | Milestone |
| [ПЗ5](pz05_break_the_model.md) | «Сломай модель» | Сценарий деградации модели | 2 %, индив. |

---

## Связь с предыдущими работами

Финальный этап сквозного проекта:

- **Модель:** `models/best_model/` из [ЛР6](../M3-neural-networks/lab06_validation_hyperparameters.md).
- **Данные:** симуляция дрейфа строится на `load_daily_clean.csv`.
- **Результат семестра:** контейнеризованный сервис прогноза с CI/CD и протоколом мониторинга.

---

## Рекомендуемая последовательность

```
ЛР6 (model) → ЛР7 (drift) → П1 (API) → П2 (Docker) → П3 (monitoring) → ЛР8 (CI/CD + защита)
```

## Файлы

| Файл | Описание |
|------|----------|
| [practice01_rest_api.md](practice01_rest_api.md) | Практика 1: REST API |
| [practice02_containerization.md](practice02_containerization.md) | Практика 2: Docker и воспроизводимость |
| [practice03_monitoring.md](practice03_monitoring.md) | Практика 3: мониторинг |
| [practice_rubrics.md](practice_rubrics.md) | Сводные критерии практик |
| [lab07_stability_drift.md](lab07_stability_drift.md) | ЛР7: устойчивость и дрейф |
| [lab08_containerization_cicd.md](lab08_containerization_cicd.md) | ЛР8: CI/CD и защита |
| [pz05_break_the_model.md](pz05_break_the_model.md) | ПЗ5 |
| [pz05_key.md](pz05_key.md) | Ключ ПЗ5 |
| [instructor_guide.md](instructor_guide.md) | Методические рекомендации |
