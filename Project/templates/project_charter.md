# Устав проекта (Project Charter)

> Один репозиторий на команду; ЛР1–10 = milestones проекта.  
> Заполните в **недели 1–2**. Согласуйте с преподавателем и представителем партнёра.  
> Подробнее: [project_guidelines.md](../project_guidelines.md)

---

## 1. Общие сведения

| Поле | Значение |
|------|----------|
| **Название проекта** | |
| **Дисциплина** | Прогнозирование временных рядов: от статистики до MLOps |
| **Команда** | ФИО участников (3–4 чел.) |
| **Индустриальный партнёр** | Организация, контактное лицо, email |
| **Преподаватель** | |
| **Дата kick-off** | |
| **Репозиторий** | URL |
| **Временной ряд / кейс** | (ENTSO-E / данные партнёра — указать) |

---

## 2. Постановка задачи

### Бизнес-контекст

*(2–4 предложения: какой процесс у партнёра, почему нужен прогноз)*

### Цель проекта

*(Измеримая цель MVP к концу семестра)*

**Пример:** «Разработать MVP сервиса прогноза суточного спроса на SKU-X с MAPE ≤ 15 % на горизонте 7 дней.»

### Горизонт прогноза

- Шаг: (час / сутки / неделя)
- Горизонт $h$ = ___ шагов

### Метрика успеха

| Метрика | Целевое значение | Комментарий |
|---------|------------------|-------------|
| MAE / RMSE / MAPE / SMAPE | | |
| Бизнес-метрика (опционально) | | |

---

## 3. Данные

| Параметр | Значение |
|----------|----------|
| **Источник** | (ERP партнёра / API / CSV / ENTSO-E) |
| **Целевая переменная** | |
| **Дополнительные признаки** | |
| **Период истории** | с ___ по ___ |
| **Периодичность** | |
| **NDA** | да / нет |
| **Можно ли публиковать в open repo** | да / нет / только агрегаты |
| **Ответственный (Data Analyst)** | ФИО |

### Ограничения и риски данных

- 
- 

---

## 4. Scope MVP

### В scope (обязательно — milestones ЛР1–10 + PRC)

- [ ] ЛР1–2: EDA и предобработка (`data/processed/`)
- [ ] ЛР3–6: baseline + статистика + регрессия + ML
- [ ] ЛР7–8: нейросеть + validation + `models/best_model/` + drift rules
- [ ] ЛР9–10: REST API, `docker-compose.yml` (FastAPI + MLflow), мониторинг
- [ ] COP: submission + отчёт
- [ ] PRC: live demo + peer-review

### Out of scope (не делаем в семестре)

*(Например: Kubernetes, real-time streaming, mobile app)*

- 
- 

---

## 5. Роли команды

### 5.1. Назначение ролей

| Участник | Основная роль | Вспомогательная (опц.) | GitHub / email |
|----------|---------------|------------------------|----------------|
| | Data Analyst | | |
| | ML Engineer | | |
| | ML Researcher | | |
| | MLOps | | |

*При 3 участниках роль **ML Researcher** совмещается с ML Engineer или Data Analyst.*

**Координатор команды** (устав, встречи, финальная презентация): ФИО ___

### 5.2. Зоны ответственности

| Роль | Зона | Ключевые артефакты |
|------|------|-------------------|
| **Data Analyst** | M1: данные, EDA, preprocessing | `data/`, `notebooks/01_*`, `src/preprocessing.py` |
| **ML Engineer** | Код моделей, пайплайны, инференс | `src/*_models.py`, `models/` |
| **ML Researcher** | Валидация, эксперименты, Optuna, drift | `docs/validation.md`, `src/drift_analysis.py`, COP |
| **MLOps** | API, Docker, CI/CD, MLflow, мониторинг | `docker-compose.yml`, `app/`, `monitoring/` |

### 5.3. RACI по milestones (заполнить ФИО или роль)

**R** — исполнитель · **A** — ответственный за сдачу · **C** — консультант · **I** — в курсе

| Milestone | R | C | I |
|-----------|---|---|---|
| ЛР1 EDA | Data Analyst | ML Engineer | MLOps |
| ЛР2 Preprocessing | Data Analyst | ML Engineer | MLOps |
| ЛР3 Statistical | ML Engineer | ML Researcher | MLOps |
| ЛР4 Stat diagnostics | ML Researcher | ML Engineer | MLOps |
| ЛР5 Regression | ML Engineer | Data Analyst | MLOps |
| ЛР6 ML | ML Engineer | ML Researcher | MLOps |
| ЛР7 Neural | ML Engineer | ML Researcher | MLOps |
| ЛР8 Validation / drift | ML Researcher, MLOps | Data Analyst | — |
| ЛР9 Deploy | MLOps | ML Engineer | Data Analyst |
| ЛР10 Monitoring | MLOps, ML Researcher | ML Engineer | — |
| COP | ML Researcher | ML Engineer | MLOps |
| PRC | MLOps (demo) | все | — |

### 5.4. Правила совместной работы

- [ ] Stand-up: ___ (день/время), фасилитация — **координатор**
- [ ] PR: минимум 1 review от другой роли
- [ ] Ветки: `feature/<role>-<task>` или по договорённости
- [ ] Commit messages: `[LR01]`, `[LR02]`, … для трассировки milestone

---

## 6. Календарь milestones

| Неделя | Milestone | Ответственный (R) | Deliverable | Срок (план) |
|--------|-----------|-------------------|-------------|-------------|
| 1–2 | M0: Устав | Координатор | Этот документ | |
| 2–3 | **ЛР1** EDA | Data Analyst | `notebooks/01_eda.ipynb`, `docs/report/01_eda.md` | |
| 3–4 | **ЛР2** Preprocessing | Data Analyst | `data/processed/`, `src/preprocessing.py` | |
| 5–6 | **ЛР3** Statistical | ML Engineer | `src/stat_models.py`, `models/sarima/` | |
| 6–7 | **ЛР4** Stat diagnostics | ML Researcher | `src/stat_models_param.py` | |
| 7–8 | **ЛР5** Regression | ML Engineer | `src/r_models.py`, `data/features/` | |
| 8–9 | **ЛР6** ML | ML Engineer | `src/ml_models.py` | |
| 9–10 | **ЛР7** Neural | ML Engineer | `src/neural_models.py`, `models/lstm/` | |
| 10–11 | **ЛР8** Validation / drift | ML Researcher + MLOps | `docs/validation.md`, `models/best_model/` | |
| 10–11 | **COP** Forecast Cup | ML Researcher | submission + отчёт | |
| 12–13 | **ЛР9** Deploy | MLOps | `docker-compose.yml`, CI, MLflow | |
| 13–14 | **ЛР10** Monitoring | MLOps + ML Researcher | `monitoring/`, `docs/monitoring.md` | |
| 15–16 | **PRC** + peer-review | Координатор + все | Презентация, demo, отчёт | |

*Даты уточняются преподавателем. ПЗ1–5 — индивидуально, параллельно milestones.*

---

## 7. Коммуникация

| Формат | Частота | Участники | Ответственный |
|--------|---------|-----------|---------------|
| Stand-up команды | еженедельно | команда | Координатор |
| Sync с партнёром | раз в 2 недели | Координатор + партнёр | Координатор |
| Review с преподавателем | по milestone ЛР | команда | Координатор |
| Протокол встречи | после sync | — | Координатор → `docs/meetings/` |

**Канал связи:** (Telegram / email / …)

---

## 8. Подписи

| Роль | ФИО | Дата |
|------|-----|------|
| Data Analyst | | |
| ML Engineer | | |
| ML Researcher | | |
| MLOps | | |
| Координатор (если не совмещён) | | |
| Преподаватель | | |
| Партнёр (опционально) | | |

*При 3 участниках — строки по фактическим ролям.*
