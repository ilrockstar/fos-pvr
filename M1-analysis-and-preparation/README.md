# Модуль 1. Анализ и подготовка временных рядов

**Формат ФОС:** ЛР1–2 — **milestones группового проекта** (команда 3–4 чел., один репозиторий).  
**ПЗ модуля:** ПЗ1–ПЗ3 (индивидуально, 6 % суммарно); ПЗ1 — пререквизит ML на старте M1.

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Компетенции КРМ:** ML-3.1, ML-2.2, ML-2.3, BD-1.2, BD-1.3

---

## Состав модуля

| ЛР | Тема | Содержание | Форма контроля |
|----|------|------------|----------------|
| [ЛР1](lab01_eda.md) | EDA временного ряда | Структура ряда, тренд, сезонность, ACF/PACF | Milestone, отчёт |
| [ЛР2](lab02_preprocessing_decomposition.md) | Предобработка и декомпозиция | … | Milestone, отчёт |
| [ПЗ1](quiz_pz1_ml_basics.md) | Квиз «Остаточные знания по ML» | пререквизит ML (M1) | 2 %, индив. |
| [ПЗ2](quiz_pz01_structure.md) | Квиз «Структура ряда» | тренд, сезонность, ACF | 2 %, индив. |
| [ПЗ3](quiz_pz02_stationarity.md) | Квиз «Стационарность» | log/diff, ADF/KPSS | 2 %, индив. |

---

## Сквозной сценарий семестра

Все **десять** milestones выполняет **команда 3–4 чел.** на **одном наборе данных** — суточном потреблении электроэнергии (регион ENTSO-E). Общий репозиторий и отчёт; см. [Project/README.md](../Project/README.md) и [docs/grading.md](../docs/grading.md).

```
ЛР1–2 (M1) → ЛР3–6 (M2) → ЛР7–8 (M3) → ЛР9–10 (M4) → PRC
```

Подробнее об источнике данных — в [data_sources.md](data_sources.md).

---

## Файлы

| Файл | Описание |
|------|----------|
| [lab01_eda.md](lab01_eda.md) | Лабораторная работа 1: EDA |
| [lab02_preprocessing_decomposition.md](lab02_preprocessing_decomposition.md) | Лабораторная работа 2: предобработка и декомпозиция |
| [quiz_pz1_ml_basics.md](quiz_pz1_ml_basics.md) | ПЗ1: остаточные знания ML |
| [quiz_pz01_structure.md](quiz_pz01_structure.md) | ПЗ2: структура ряда |
| [quiz_pz02_stationarity.md](quiz_pz02_stationarity.md) | ПЗ3: стационарность |
| [quiz_pz1_key.md](quiz_pz1_key.md) | Ключ ПЗ1 |
| [quiz_pz01_key.md](quiz_pz01_key.md) | Ключ ПЗ2 |
| [quiz_pz02_key.md](quiz_pz02_key.md) | Ключ ПЗ3 |
| [data_sources.md](data_sources.md) | Описание сквозного датасета и альтернативных источников |
| [instructor_guide.md](instructor_guide.md) | Методические рекомендации преподавателю |
