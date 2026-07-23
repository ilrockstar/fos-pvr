# Forecast Cup — руководство организатора

## 1. Подготовка данных

### Разрез train / test

1. Взять `load_daily_clean.csv` (или ENTSO-E daily load ≥ 4 лет).
2. Выбрать `TRAIN_END` — например, `2026-01-31`.
3. Test period: **60 суток** после `TRAIN_END`.
4. Выгрузить:
   - `train.csv` — только `date <= TRAIN_END`
   - `sample_submission.csv` — даты test, `load_mwh=0`
   - `solution.csv` — **скрытый**, test с истинными $y$

### README_data.md (шаблон)

```markdown
# Forecast Cup — данные
TRAIN_END: 2026-01-31
TEST_DAYS: 60
Target: load_mwh (daily MWh)
Forbidden: full dataset with test labels
```

---

## 2. Регистрация команд

| Поле | |
|------|---|
| team_name | |
| members (2–4) | |
| email lead | |
| submissions_count | 0 |

Google Sheet или LMS assignment.

---

## 3. Приём сабмитов

1. Автоответ: «получено, проверка в течение 24 ч».
2. Запустить `validate_submission()` из [scoring.md](scoring.md).
3. При OK — `score_submission()` → запись в leaderboard.
4. Public LB обновлять optional; **финал только private**.

---

## 4. Расчёт baselines

Запустить до старта (на public slice или full train→val):

```python
# naive, seasonal naive — см. baselines.md
# SARIMA / RF — эталонные ноутбуки преподавателя
```

Опубликовать SMAPE baselines в LMS в день старта.

---

## 5. Расписание

| День | Действие |
|------|----------|
| D0 | Kick-off: правила, выдача data, регистрация |
| D+7 | Mid-check: сколько команд сдали ≥1 сабмит |
| D+14 | Deadline 18:00, закрыть приём |
| D+15 | Private LB, письмо результатов |
| D+16 | Мини-защита топ-5 (5 мин) |

---

## 6. Защита топ-команд (5 мин)

Вопросы:

1. Архитектура neural — слои, lookback, dropout?
2. Как валидировали? Где test COP не участвовал?
3. Почему SMAPE / MAE такие — главная ошибка модели?
4. LSTM vs GRU / ablation?
5. Что бы сделали ещё 2 дня?

---

## 7. Оценивание и КРМ

| Компонент | Max | Кто ставит |
|-----------|-----|------------|
| Rank (private SMAPE) | 50 | скрипт + таблица |
| Отчёт | 25 | преподаватель |
| Reproducibility | 15 | преподаватель |
| Beat naive | 10 | скрипт |

Уровень **П** (ML-3.2): Top-3 или в пределах 5 % SMAPE от лучшей + отличный отчёт.

---

## 8. Типичные проблемы

| Проблема | Решение |
|----------|---------|
| Все submission одинаковые | Проверить plagiarism, seed-only diff |
| SMAPE > 50 % | Неверный scale / не inverse_transform |
| Команда без neural | Не зачёт COP, направить на доработку ЛР7 |
| Late submit | −10 % rank-баллов за каждые сутки (настройка) |

---

## 9. Файлы для выкладки студентам

```
competition/data/train.csv
competition/data/sample_submission.csv
competition/data/README_data.md
../competition.md          # правила
submission_checklist.md
baselines.md               # после D0
```

**Не выкладывать:** `solution.csv`

---

## 10. Связь с СОР дисциплины

Forecast Cup = **COP** по учебному плану (**15 %** дисциплины, [grading.md](../../docs/grading.md)). Зачёт COP — при выполнении порога «Базовый».

ЛР7–8 могут использовать **тот же код**, что COP, но оцениваются отдельно по рубрикам лабораторных.
