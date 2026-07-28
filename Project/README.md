# Проектная работа (PRC) — финальная защита MVP

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Архитектура ФОС:** PRC = финальная защита **того же** группового проекта, что и milestones **ЛР1–10**  
**Состав команды:** 3–4 человека  
**Компетенции:** все 10 индикаторов четырёх компетенций КРМ (уровень **С**)

---

## 1. Концепция и правила формирования команд

Сквозной **коллективный проект** выполняется в течение всего семестра группами по **3–4 человека**.  
Команда строит **полноценную воспроизводимую прогнозную систему** для реального временного ряда индустриального уровня (**УГТ 6** — прототип, демонстрируемый в реальной среде).

```
Команда 3–4 чел. → один репозиторий → один временной ряд / кейс
        ↓
ЛР1…ЛР10 = milestones (отчёты + модули кода)
        ↓
COP (Forecast Cup) = соревнование на том же или расширенном датасете
        ↓
PRC = защита целостного MVP + peer-review
```

| Элемент | Что это |
|---------|---------|
| **Milestone ЛР** | Раздел отчёта + модуль в `src/` / `notebooks/` |
| **Защита ЛР** | Промежуточная презентация прогресса **команды** |
| **PRC** | Финальная защита **целостного** Docker-сервиса с мониторингом |

---

## 2. О проекте

Команда проходит полный цикл на **едином временном ряде** (рекомендуется: суточная нагрузка ENTSO-E или кейс индустриального партнёра по согласованию) и к финалу сдаёт **MVP**: REST API + Docker + MLflow + мониторинг drift + документация.

**Вес PRC:** 35 % (защита MVP × \(k_{peer}\) из peer-review). См. [docs/grading.md](../docs/grading.md).

### Профессиональные роли в команде

Каждая команда **явно распределяет** роли КРМ:

| Роль | Зона ответственности |
|------|----------------------|
| **Data Analyst** | M1: EDA, очистка, декомпозиция, генерация признаков (ЛР1–2) |
| **ML Researcher** | Валидация гипотез, сравнение ARIMA / ML / DL, тюнинг в Optuna (ЛР4, ЛР8, COP) |
| **ML Engineer** | Продакшен-код моделей, оптимизация инференса и пайплайнов (ЛР3–7) |
| **MLOps** | Docker, CI/CD, MLflow, Airflow/Prefect, дашборды мониторинга drift (ЛР9–10) |

> **Команда из 3 человек:** роль **ML Researcher** совмещается с ML Engineer или Data Analyst (фиксируется в [уставе](templates/project_charter.md)).

---

## 3. Жизненный цикл проекта

Лабораторные работы — **не изолированные задачи**, а **milestones (вехи)** сквозного проекта.

Подробные методические рекомендации по каждой продуктовой вехе — в [Project/attachments/](attachments/) ([p_v1.pdf](attachments/p_v1.pdf) … [p_v4.pdf](attachments/p_v4.pdf)).

| Веха | Недели | Содержание | Milestones | Методич. PDF |
|------|--------|------------|------------|--------------|
| **1. Исследование** | 1–4 | EDA, очистка, STL, ADF/KPSS, `preprocessing.py` | [ЛР1–2](../M1-analysis-and-preparation/) | [p_v1.pdf](attachments/p_v1.pdf) |
| **2. Stat + ML** | 5–8 | ARIMA/SARIMA, feature pipeline, CatBoost/LightGBM, Optuna | [ЛР3–6](../M2-statistical-and-ML-models/) | [p_v2.pdf](attachments/p_v2.pdf) |
| **3. DL + устойчивость** | 9–12 | LSTM/GRU, многошаговый прогноз, drift-стресс-тест | [ЛР7–8](../M3-neural-networks/), COP | [p_v3.pdf](attachments/p_v3.pdf) |
| **4. MLOps / деплой** | 13–16 | FastAPI, Docker, MLflow, мониторинг, auto-retrain | [ЛР9–10](../M4-infrastructure-and-deployment/), PRC | [p_v4.pdf](attachments/p_v4.pdf) |

### Кратко по вехам

**Веха 1 — разведочный анализ и верификация** ([p_v1.pdf](attachments/p_v1.pdf)): команда сдаёт воспроизводимый пайплайн очистки и первичного анализа — импорт и аудит сырого ряда, фильтрация выбросов и лакун без data leakage, STL-декомпозиция, ACF/PACF, тесты ADF/KPSS. Роли: Data Analyst (EDA и отчёт), ML Engineer (функции очистки), MLOps (venv, структура repo).

**Веха 2 — статистика и классическое ML** ([p_v2.pdf](attachments/p_v2.pdf)): на базе `preprocessing.py` — идентификация ARIMA/SARIMA, аудит остатков, автоматизированный feature engineering (лаги, rolling, календарь, exog), TimeSeriesSplit, градиентный бoosting и Optuna; сводная таблица stat vs ML.

**Веха 3 — нейросети и устойчивость** ([p_v3.pdf](attachments/p_v3.pdf)): тензоризация данных, обучение LSTM/GRU (или Conv1D), многошаговый прогноз, симуляция индустриального drift и сравнение с ML-baseline из вехи 2; параллельно — Forecast Cup.

**Веха 4 — инфраструктура и prod** ([p_v4.pdf](attachments/p_v4.pdf)): сериализация лучшей модели, REST API (FastAPI + pydantic), Docker-образ, MLflow, скрипт-монитор drift и автоматический retrain-тригger; финальная защита MVP (PRC).

\* Prophet — опционально, если обоснован в постановке задачи.

---

## 4. Требования к итоговому продукту (MVP)

Финальный проект сдаётся как **публичный репозиторий** (структура — по аналогии с ФОС курса) и содержит:

1. **`data/` и `notebooks/`** — задокументированный воспроизводимый EDA.
2. **`docker-compose.yml`** — по команде `docker-compose up --build` автоматически поднимаются:
   - сервис прогнозирования (**FastAPI**);
   - сервер отслеживания экспериментов (**MLflow**).
3. **Скрипт симуляции** — демонстрация реакции системы на искажённые данные (связь с [ПЗ5](../M4-infrastructure-and-deployment/pz05_break_the_model.md) и `monitoring/simulate_drift.py`).
4. **Инженерно-технический отчёт** — бизнес-контекст; обоснование метрик (MAE / MAPE / quantile loss при асимметрии); сводная таблица экспериментов; описание MLOps-архитектуры.

Подробнее: [project_guidelines.md](project_guidelines.md) · [rubrics.md](rubrics.md).

---

## 5. Оценивание и защита

Проект формирует **35 %** итоговой оценки (PRC + peer-review) и **40 %** опосредованно — через защиту milestones ЛР1–10.

$$\text{Балл}_{PRC\_студента} = 35\% \times \frac{B_{комиссии}}{100} \times k_{peer}$$

**Распределение баллов комиссии (100 % за PRC):**

| Критерий | Вес |
|----------|-----|
| Полнота функциональности MVP | 30 % |
| Качество кода и архитектура системы | 20 % |
| Мониторинг, устойчивость и ретренинг | 20 % |
| Презентация и доклад (регламент **10 мин**) | 15 % |
| Техническая документация | 15 % |

**Peer-review:** анонимная взаимооценка по 4 критериям → коэффициент **\(k_{peer}\)** 0,7–1,3. См. [peer_review.md](peer_review.md).

### Форма контроля PRC

- Финальная презентация (12–15 слайдов)
- Live demo MVP
- [Peer-review](peer_review.md)
- Итоговый отчёт (накопленный из milestones)

---

## 6. Состав материалов

| Файл | Описание |
|------|----------|
| [project_guidelines.md](project_guidelines.md) | Этапы, роли, артефакты, MVP |
| [peer_review.md](peer_review.md) | Взаимооценка (\(k_{peer}\) к блоку 35 %) |
| [instructor_guide.md](instructor_guide.md) | Организация для преподавателя |
| [rubrics.md](rubrics.md) | Критерии защиты PRC |
| [templates/](templates/) | Устав, milestone, презентация |
| [attachments/](attachments/) | **Ключевые вехи** — методические PDF ([p_v1](attachments/p_v1.pdf)–[p_v4](attachments/p_v4.pdf)) |

---

## 7. Связь milestones ↔ ЛР

| Milestone | ЛР | Артефакт в репозитории команды |
|-----------|-----|--------------------------------|
| EDA | ЛР1 | `notebooks/01_eda.ipynb`, `docs/report/01_eda.md` |
| Preprocessing | ЛР2 | `data/processed/`, `src/preprocessing.py` |
| Statistical | ЛР3 | `src/stat_models.py`, `models/sarima/` |
| Stat diagnostics | ЛР4 | `src/stat_models_param.py` |
| Regression | ЛР5 | `src/r_models.py` |
| ML | ЛР6 | `src/ml_models.py` |
| Neural | ЛР7 | `src/neural_models.py`, `models/lstm/` |
| Validation / drift | ЛР8 | `docs/validation.md`, `models/best_model/` |
| Deploy / CI | ЛР9 | `docker-compose.yml`, FastAPI, MLflow, CI |
| Monitoring | ЛР10 | `monitoring/`, `docs/monitoring.md` |
