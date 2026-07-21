# Практические задания и квизы (ПЗ1–ПЗ5)

**Общий вес в дисциплине:** 10 % (по 2 % каждое)  
**Архитектура:** вариант A — выполняются **индивидуально**; проект команды оценивается отдельно.

> Тематика и недели согласованы с [presentation_itmo.pdf](../presentation_itmo.pdf) (слайд 13). Квизы **короткие** (6 вопросов, ~10–15 мин), без перегрузки формулами — глубина в milestone ЛР.

---

## График проведения

| ПЗ | Неделя | Название | Модуль | Время | Комpetence | Роль КРМ |
|----|--------|----------|--------|-------|------------|----------|
| [ПЗ1](../M1-analysis-and-preparation/quiz_pz1_ml_basics.md) | 1 | Квиз «Остаточные знания по ML» | — | 10 мин | ML-3.1, BD 1.2 | все |
| [ПЗ2](../M1-analysis-and-preparation/quiz_pz01_structure.md) | 2 | Квиз «Структура ряда» | M1 | 10 мин | BD 1.2 | Data Analyst |
| [ПЗ3](../M1-analysis-and-preparation/quiz_pz02_stationarity.md) | 4 | Квиз «Стационарность» | M1 | 15 мин | BD 1.3 | Data Analyst |
| [ПЗ4](../M2-statistical-and-ML-models/quiz_pz03_metrics.md) | 7 | Квиз «Метрики качества» | M2 | 15 мин | ML-2.2 | ML Engineer / Researcher |
| [ПЗ5](../M4-infrastructure-and-deployment/pz05_break_the_model.md) | 15 | Кейс «Сломай модель» | M4 | дома | ML-2.2, ML-5.2 | ML Researcher / MLOps |

### Содержание по презентации

| ПЗ | Что проверяем (кратко) |
|----|------------------------|
| ПЗ1 | Переобучение, train/test, тип задачи, базовые алгоритмы, MAE |
| ПЗ2 | Тренд, сезонность, ACF «на пальцах», аномалии |
| ПЗ3 | Стационарность, log/diff, ADF/KPSS на уровне идеи, пропуски |
| ПЗ4 | MAE, RMSE, MAPE/SMAPE, выбор метрики, train vs test |
| ПЗ5 | Сценарий деградации + простая политика retraining |

---

## Ключи (только преподавателю)

| ПЗ | Ключ |
|----|------|
| ПЗ1 | [quiz_pz1_key.md](../M1-analysis-and-preparation/quiz_pz1_key.md) |
| ПЗ2 | [quiz_pz01_key.md](../M1-analysis-and-preparation/quiz_pz01_key.md) |
| ПЗ3 | [quiz_pz02_key.md](../M1-analysis-and-preparation/quiz_pz02_key.md) |
| ПЗ4 | [quiz_pz03_key.md](../M2-statistical-and-ML-models/quiz_pz03_key.md) |
| ПЗ5 | [pz05_key.md](../M4-infrastructure-and-deployment/pz05_key.md) |

---

## Формативные материалы (не входят в 10 %)

| Материал | Назначение |
|----------|------------|
| [M1/quiz.md](../M1-analysis-and-preparation/quiz.md) | Короткая самопроверка M1 (~6 вопросов) |
| [M2/quiz.md](../M2-statistical-and-ML-models/quiz.md) | К ЛР5: лаги, календарь, exog (~6 вопросов) |
| [M3/quiz_pz04_drift.md](../M3-neural-networks/quiz_pz04_drift.md) | Самопроверка drift к ЛР8 |

---

## Перевод в баллы

Каждый квиз ПЗ1–4: **max 30 баллов** → `(набрано / 30) × 100` → **2 %** дисциплины.  
ПЗ5: уровни 60 / 80 / 100 → **2 %**. См. [grading.md](grading.md), раздел 3.
