# Forecast Cup — описание данных (шаблон для организатора)

> Скопируйте в `competition/data/README_data.md` и заполните `TRAIN_END` перед стартом.

---

## Источник

Суточное потребление электроэнергии (ENTSO-E / сквозной датасет курса), агрегированное до 1 obs = 1 day.

## Периоды

| Split | Даты | Файл |
|-------|------|------|
| Train (public) | `____-__-__` … `TRAIN_END` | `train.csv` |
| Test (private labels) | `TRAIN_END + 1 day` … (+60 days) | только в `sample_submission.csv` |

**TRAIN_END:** `YYYY-MM-DD`  
**TEST_DAYS:** 60

## Переменные

| Column | Type | Description |
|--------|------|-------------|
| date | datetime | UTC date, daily |
| load_mwh | float | Total daily load, MWh |

## Запреты

- Не использовать полный `load_daily_clean.csv` с метками test-периода.
- Не делиться `solution` между командами.
- Внешние данные — только с одобрения преподавателя (список в LMS).

## Контакт

Преподаватель: ___  
Email для submission: ___
