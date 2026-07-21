# Методические указания преподавателям по использованию ресурсов

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps

---

## 1. Назначение раздела

Рекомендации по подбору и применению материалов при проведении занятий и разработке/адаптации КИМ.

Каталог ресурсов: [resources/README.md](../../resources/README.md).

---

## 2. Обязательные ресурсы семестра

| Ресурс | Когда использовать |
|--------|-------------------|
| [M1/data_sources.md](../../M1-analysis-and-preparation/data_sources.md) | Старт ЛР1; единый ряд ENTSO-E |
| [data/krm-v3.0.xlsx](../../data/krm-v3.0.xlsx) | Верификация кодов competence в rubrics |
| [docs/measurement_model.md](../../docs/measurement_model.md) | Заполнение ведомостей, защита программы |
| [Project/project_guidelines.md](../../Project/project_guidelines.md) | Kick-off проекта, роли команд |

---

## 3. Подбор материалов по модулям

| Модуль | Рекомендуемые внешние источники | Связь с КИМ |
|--------|--------------------------------|-------------|
| M1 | FPP3 (Hyndman), главы 2–3 | ЛР1–2, ПЗ2–3 |
| M2 | Statsmodels TSA, sklearn time series | ЛР3–6, ПЗ4 |
| M3 | PyTorch RNN tutorials, Optuna docs | ЛР7–8, COP |
| M4 | FastAPI, Docker, MLflow docs | ЛР9–10, ПЗ5 |

Перед семестром проверьте **работоспособность URL** из [resources/README.md](../../resources/README.md).

---

## 4. Индустриальный партнёр

- Кейс партнёра должен удовлетворять критериям в [Project/instructor_guide.md §2](../../Project/instructor_guide.md).
- Альтернатива без партнёра: ENTSO-E + proxy-бизнес-постановка в уставе.
- NDA и хранение данных — ответственность команды (**Data Analyst** + координатор).

---

## 5. Адаптация КИМ

При изменении задания обновите:

1. Критерии в файле ЛР / rubrics.
2. Строку в [measurement_model.md](../../docs/measurement_model.md) (если меняется competence).
3. Веса — только согласованно с [grading.md](../../docs/grading.md).
4. [quality-checklist.md](../../docs/quality-checklist.md) перед релизом ФОС.

---

## 6. Лицензии внешних материалов

- Учебники с открытым доступом (FPP3) — CC BY-NC для FPP; указывать в силлабусе.
- Код примеров из документации — следовать лицензии проекта (MIT/BSD).
- Данные ENTSO-E — [Transparency Platform terms](https://transparency.entsoe.eu/).

Материалы репозитория ФОС: [LICENSE.md](../../LICENSE.md) (CC BY 4.0).
