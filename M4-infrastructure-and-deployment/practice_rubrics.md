# Сводная таблица: практические занятия модуля 4

| Практика | Тема | Время | Баллы (рекомендация) |
|----------|------|-------|----------------------|
| [П1](practice01_rest_api.md) | REST API | 1+1 ч | 30 |
| [П2](practice02_containerization.md) | Docker, воспроизводимость | 1+1 ч | 30 |
| [П3](practice03_monitoring.md) | Мониторинг, MLflow | 2+2 ч | 40 |

**Итого практика:** 100 баллов → нормализация по регламенту программы.

## Рекомендуемая последовательность

```
П1 (API контракт) → П2 (Docker) → П3 (logs + metrics) → ЛР8 (CI/CD) → защита
         ↑                                    ↑
    ЛР6 model                          ЛР7 drift playbook
```

## Связь с лабораторными

| Практика | Лабораторная | Отличие |
|----------|--------------|---------|
| П1–П2 | ЛР8 | Практика — пошаговая отработка на паре; ЛР8 — интегральная сдача с CI |
| П3 | ЛР7 | ЛР7 — drift/retrain offline; П3 — observability **в runtime** API |

## Оценивание уровней (сводно)

| Уровень | П1 | П2 | П3 |
|---------|----|----|-----|
| **Б** | health + predict + 2 tests | docker build + DEPLOY | JSON logs + metrics.md |
| **С** | model/info, 422, OpenAPI | compose, pinned deps | /metrics latency, MLflow |
| **П** | 503, 4+ tests | reproducibility checklist | playbook + canary |

## Типичные ошибки

| Ошибка | Где ловить |
|--------|------------|
| Модель не в образе | П2 — predict падает в контейнере |
| Логируют full history с персональными данными | П3 — review logging policy |
| `:latest` без версии | П2 — нет rollback |
| MLflow run без params | П3 — невозможно сравнить эксперименты |
