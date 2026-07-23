# Методические рекомендации к выполнению проектной работы

**Дисциплина:** Прогнозирование временных рядов: от статистики до MLOps  
**Архитектура ФОС:** ЛР1–10 = milestones **одного** группового проекта; PRC = финальная защита MVP  
**Формат:** командный кейс (PRC) на данных индустриального партнёра (или согласованном учебном ряде)  
**Команда:** 3–4 обучающихся · **один репозиторий** · общий отчёт  
**Целевой уровень продукта:** **УГТ 6** — воспроизводимый прототип прогнозной системы, демонстрируемый в реальной среде

---

## 1. Цель и задачи проекта

### Цель

Сформировать у обучающихся навык **полного цикла разработки прогнозной системы** на реальных данных: от постановки задачи и EDA до развёртывания **MVP** (REST API, Docker, MLflow, мониторинг drift) и обоснования решений перед комиссией и индустриальным партнёром.

### Задачи

1. Согласовать **предметную постановку**: что прогнозируем, горизонт, допустимая ошибка, бизнес-контекст.
2. Провести **EDA и подготовку** данных (M1): STL, стационарность, feature engineering.
3. Построить и сравнить **классические, ML- и DL-модели** (M2–M3): ARIMA, boosting, LSTM/GRU.
4. Организовать **walk-forward валидацию**, HPO (Optuna) и анализ **drift**.
5. Развернуть **MVP**: `docker-compose up --build` поднимает FastAPI + MLflow.
6. Продемонстрировать **устойчивость** (скрипт симуляции искажённых данных) и **retraining**.
7. Защитить проект и пройти [peer-review](peer_review.md).

---

## 2. Проект и лабораторные

**Нет отдельных индивидуальных лабораторных.** **Десять** ЛР — **контрольные точки (milestones)** одного сквозного проекта.

| Аспект | Как устроено |
|--------|--------------|
| Репозиторий | Один `team-<name>/` на всю команду |
| ЛР1–10 | Milestones: раздел отчёта + модуль кода (см. [grading.md](../docs/grading.md)) |
| ПЗ1–5 | **Индивидуально** — личный вклад в зачёт (10 %) |
| COP (Forecast Cup) | Соревнование; команда 2–4 чел. (может совпадать с проектной) |
| PRC + peer-review | Финальная защита MVP + взаимооценка вклада (**35 %**; \(k_{peer}\) 0,7–1,3) |

```
Устав → ЛР1…ЛР10 (milestones, 40%) → COP (15%) → PRC + peer-review (35%)
              ↑
         ПЗ1–5 параллельно (индивидуально, 10%)
```

**Данные:** единый ряд на весь семестр (ENTSO-E для старта или кейс партнёра после kick-off). Смена датасета mid-semester — только с согласования преподавателя.

### 2.1. Жизненный цикл (четыре вехи)

| Веха | Недели | Содержание | Milestones |
|------|--------|------------|------------|
| **1. Исследование** | 1–4 | Сбор данных, EDA, STL, ADF/KPSS | ЛР1–2 |
| **2. Классическое моделирование** | 5–8 | ARIMA, Prophet*, регрессия, RF/XGBoost | ЛР3–6 |
| **3. Глубокое обучение** | 9–12 | LSTM/GRU, walk-forward, Optuna, drift | ЛР7–8, COP |
| **4. Промышленный деплой** | 13–16 | FastAPI, Docker, Airflow/Prefect, Evidently | ЛР9–10, PRC |

\* Prophet — по согласованию с преподавателем, если уместен для кейса.

---

## 3. Формирование команды и роли

### 3.1. Состав

- **3–4 человека** — оптимально для параллельной работы и измеримого вклада каждого.
- Команда формируется в **недели 1–2**; роли фиксируются в [уставе](templates/project_charter.md).
- Один кейс / один временной ряд — **одна команда**.

### 3.2. Четыре роли КРМ

Роли согласованы с [peer-review](peer_review.md) и [measurement_model.md](../docs/measurement_model.md).

| Роль | Зона ответственности | Типичные артефакты |
|------|----------------------|-------------------|
| **Data Analyst** | M1: загрузка, EDA, очистка, декомпозиция, признаки | `data/`, `notebooks/01_*`, `src/preprocessing.py` |
| **ML Researcher** | Гипотезы, сравнение ARIMA/ML/DL, Optuna, drift-анализ | `docs/validation.md`, `src/drift_analysis.py`, COP-отчёт |
| **ML Engineer** | Продакшен-код моделей, пайплайны, `best_model`, инференс | `src/*_models.py`, `models/`, эксперименты |
| **MLOps** | API, Docker, CI/CD, MLflow, мониторинг, retraining | `docker-compose.yml`, `app/`, `monitoring/`, `docs/monitoring.md` |

**Правила:**

- Каждый участник имеет **одну основную роль** (можно вторую вспомогательную).
- **Координация** (устав, встречи с партнёром, сборка финальной презентации) — по договорённости в команде; фиксируется в [уставе](templates/project_charter.md).
- На защите каждого milestone и PRC участник отвечает **минимум за свою зону** (2–3 мин).
- Вклад фиксируется в Git: **≥ 15 % commits** от участника за семестр (ориентир; проверяется при peer-review).
- Роли указываются в README репозитория и на титульном слайде PRC.

### 3.3. Распределение при 3 и 4 участниках

**Команда из 4 человек** (рекомендуется):

| Участник | Роль |
|----------|------|
| A | Data Analyst |
| B | ML Engineer |
| C | ML Researcher |
| D | MLOps |

**Команда из 3 человек** — роль **ML Researcher** совмещается:

| Вариант | Участник A | Участник B | Участник C |
|---------|------------|------------|------------|
| **A** | Data Analyst (+ Researcher) | ML Engineer | MLOps |
| **B** | Data Analyst | ML Engineer (+ Researcher) | MLOps |
| **C** | Data Analyst | ML Engineer | MLOps (+ Researcher) |

Совмещение фиксируется в уставе: кто **R** (Responsible) за артефакт каждого milestone.

### 3.4. Матрица RACI: роли × milestones

**R** — исполнитель · **A** — ответственный за сдачу · **C** — консультант · **I** — в курсе

| Milestone | Data Analyst | ML Engineer | ML Researcher | MLOps | Артефакты |
|-----------|:------------:|:-----------:|:-------------:|:-----:|-----------|
| **ЛР1** EDA | **R** | I | I | I | `notebooks/01_eda.ipynb`, `docs/report/01_eda.md` |
| **ЛР2** Preprocessing | **R** | C | I | I | `data/processed/`, `src/preprocessing.py` |
| **ЛР3** Statistical | C | **R** | C | I | `src/stat_models.py`, `models/sarima/` |
| **ЛР4** Stat diagnostics | I | C | **R** | I | `src/stat_models_param.py` |
| **ЛР5** Regression | C | **R** | C | I | `src/r_models.py`, `data/features/` |
| **ЛР6** ML | C | **R** | C | I | `src/ml_models.py` |
| **ЛР7** Neural | C | **R** | C | I | `src/neural_models.py`, `models/lstm/` |
| **ЛР8** Validation / drift | C | C | **R** | **R** | `docs/validation.md`, `models/best_model/`, `src/drift_analysis.py` |
| **ЛР9** Deploy / CI | I | C | I | **R** | `docker-compose.yml`, FastAPI, MLflow, CI |
| **ЛР10** Monitoring | I | C | **R** | **R** | `monitoring/`, `docs/monitoring.md` |
| **COP** | C | C | **R** | I | submission + отчёт COP |
| **PRC** | C | C | C | **R** (demo) | MVP + презентация |

На **ЛР8** MLOps согласует формат артефакта модели для deploy; на **ЛР10** Researcher задаёт пороги drift, MLOps — observability.

### 3.5. Еженедельный ритм по ролям

| Роль | Еженедельный минимум |
|------|---------------------|
| **Координатор** (по уставу) | Stand-up (30 мин), статус партнёру раз в 2 нед. |
| **Data Analyst** | PR по данным / EDA; актуальный `data/README.md` |
| **ML Engineer** | PR по моделям; строка в таблице экспериментов |
| **ML Researcher** | PR по валидации / экспериментам; обновление `docs/validation.md` |
| **MLOps** | PR по инфраструктуре; `DEPLOY.md` актуален после ЛР9 |

**Code review:** каждый PR ревьюит **не автор**; желательно ревью от роли, которой затронут модуль.

### 3.6. Защита milestone: кто говорит

| Milestone | Докладчик (5–7 мин) | Поддержка |
|-----------|---------------------|-----------|
| ЛР1–2 | **Data Analyst** | ML Engineer — требования к признакам |
| ЛР3–6 | **ML Engineer** | ML Researcher — метрики; Data Analyst — качество данных |
| ЛР7 | **ML Engineer** | ML Researcher — сравнение с baseline |
| ЛР8 | **ML Researcher** + **MLOps** (по 2–3 мин) | Data Analyst — сценарий сдвига |
| ЛР9–10 / PRC | **MLOps** (demo) | ML Engineer — модель; Researcher — валидация |

Преподаватель может задать вопрос **любому** участнику — ответ за пределами своей роли снижает оценку milestone.

---

## 4. Работа с индустриальным партнёром

### Стартовая встреча (kick-off)

**Координатор команды** (по уставу) организует встречу; **Data Analyst** готовит вопросы по данным; **ML Researcher** — по метрикам и горизонту.

1. Какой **процесс** улучшаем прогнозом?
2. Какие **данные** доступны: история, частота, горизонт, признаки?
3. **Ограничения** (NDA, обезличивание)?
4. **Метрика успеха** для бизнеса?
5. **Контактное лицо** партнёра?

Зафиксируйте ответы в [уставе проекта](templates/project_charter.md).

### Регулярная коммуникация

- **Координатор** — не реже 1 раза в 2 недели статус партнёру.
- После каждого milestone — **демо** (Data Analyst / ML Engineer на ранних этапах, MLOps на поздних).
- Протоколы встреч — `docs/meetings/YYYY-MM-DD.md`.

### Этика и данные

- NDA по требованию партнёра; сырые данные не в публичный GitHub.
- **Data Analyst** отвечает за `.gitignore` и синтетические сэмплы для CI.
- Не загружать данные партнёра в публичные LLM.

---

## 5. Этапы проекта и milestones ЛР1–10

### Этап 0. Инициация (недели 1–2)

| Действие | Ответственный | Результат |
|----------|---------------|-----------|
| Формирование команды, роли | Координатор + преподаватель | Таблица ролей в уставе |
| Kick-off с партнёром | Координатор | Постановка, метрика |
| Репозиторий | MLOps (scaffold) + все | README, структура, branch policy |
| Устав | Координатор | [project_charter.md](templates/project_charter.md) |

---

### ЛР1 — EDA (недели 2–3) · 4 %

Опирается на [ЛР1](../M1-analysis-and-preparation/lab01_eda.md). **Data Analyst — R.**

- [ ] `notebooks/eda.ipynb`, `docs/report/01_eda.md`
- [ ] Графики ряда, сезонности, ACF/PACF
- [ ] Координатор: партнёр подтвердил соответствие ряда задаче

---

### ЛР2 — Preprocessing (недели 3–4) · 4 %

[ЛР2](../M1-analysis-and-preparation/lab02_preprocessing_decomposition.md). **Data Analyst — R**, ML Engineer — C.

- [ ] `data/processed/`, `src/preprocessing.py`, `preprocessing_meta.json`
- [ ] STL, ADF/KPSS; документированы пропуски и выбросы

**ПЗ3** (стационарность) — **индивидуально**, не заменяет milestone.

---

### ЛР3 — Statistical models (недели 5–6) · 4 %

[ЛР3](../M2-statistical-and-ML-models/lab03_classical_statistical_models.md). **ML Engineer — R.**

- [ ] Baseline + SARIMA/ES; `src/stat_models.py`
- [ ] ACF/PACF на train; кандидат для ЛР4

---

### ЛР4 — Stat diagnostics (недели 6–7) · 4 %

[ЛР4](../M2-statistical-and-ML-models/lab04_statistical_diagnostics.md). **ML Researcher — R.**

- [ ] `src/stat_models_param.py`; подбор (p,d,q)(P,D,Q,s)
- [ ] Диагностика остатков; финальная SARIMA

**ПЗ4** (метрики) — индивидуально.

---

### ЛР5 — Regression (недели 7–8) · 4 %

[ЛР5](../M2-statistical-and-ML-models/lab05_regression_factor_models.md). **ML Engineer — R**, Data Analyst — C.

- [ ] `src/r_models.py`, `data/features/`
- [ ] Лаги, календарь, exog; сравнение с SARIMA

---

### ЛР6 — ML models (недели 8–9) · 4 %

[ЛР6](../M2-statistical-and-ML-models/lab06_ml_models.md). **ML Engineer — R.**

- [ ] `src/ml_models.py`; RF / XGBoost
- [ ] Сравнение с ЛР3–5; лучший табличный baseline

---

### ЛР7 — Neural nets (недели 9–10) · 4 %

[ЛР7](../M3-neural-networks/lab07_neural_networks.md). **ML Engineer — R.**

- [ ] LSTM/GRU pipeline; `models/lstm/`

---

### ЛР8 — Validation / drift (недели 10–11) · 4 %

[ЛР8](../M3-neural-networks/lab08_validation_drift.md). **ML Researcher — R**, MLOps — C.

- [ ] Walk-forward, Optuna; `docs/validation.md`, `models/best_model/`
- [ ] `src/drift_analysis.py`; правила retraining

**COP (Forecast Cup)** — **ML Researcher — R** submission; ML Engineer — модель; координатор — отчёт COP.

---

### ЛР9 — Deploy / CI (недели 12–13) · 4 %

[ЛР9](../M4-infrastructure-and-deployment/lab09_containerization_cicd.md). **MLOps — R.**

- [ ] REST API, `docker-compose.yml` (FastAPI + MLflow), CI; `services/ml_service/` или `app/`
- [ ] Smoke-test: преподаватель запускает за ≤ 5 мин

---

### ЛР10 — Monitoring (недели 13–14) · 4 %

[ЛР10](../M4-infrastructure-and-deployment/lab10_monitoring.md). **MLOps + ML Researcher — R.**

- [ ] `monitoring/`, Evidently drift-report, DAG/Prefect flow, `docs/monitoring.md`
- [ ] Метрики inference, алерты drift (пороги из ЛР8)

**ПЗ5** («сломай модель») — индивидуально.

---

### PRC — финальная защита (недели 15–16) · 35 %

| Артефакт | Ответственный |
|----------|---------------|
| Презентация 12–15 слайдов (регламент **10 мин**) | Координатор (сборка), все — спикеры |
| Live demo MVP | MLOps |
| Итоговый отчёт 15–25 стр. | Координатор (сборка) + разделы по ролям |
| Peer-review | каждый — анкеты на n−1 коллег | 

Структура слайдов: [defense_outline.md](templates/defense_outline.md).

---

## 6. Структура репозитория проекта

```
project-team-name/
├── README.md                 # Кейс, команда, роли, quick start
├── docs/
│   ├── project_charter.md    # Устав + таблица ролей
│   ├── report/               # 01_eda.md … 10_monitoring.md (milestones)
│   ├── validation.md
│   ├── monitoring.md
│   └── meetings/
├── data/
│   ├── raw/
│   ├── processed/
│   └── README.md             # Data Analyst
├── notebooks/
├── src/
│   ├── preprocessing.py      # Data Analyst
│   ├── stat_models.py        # ML Engineer
│   ├── stat_models_param.py  # ML Researcher
│   ├── r_models.py
│   ├── ml_models.py
│   ├── neural_models.py
│   └── drift_analysis.py     # ML Researcher
├── app/                        # MLOps
├── models/best_model/
├── monitoring/               # simulate_drift.py, Evidently
├── tests/
├── docker-compose.yml        # FastAPI + MLflow (обязательно)
├── Dockerfile
├── DEPLOY.md
└── .github/workflows/ci.yml
```

---

## 7. Минимальные требования к MVP

Итоговый продукт — **публичный репозиторий** с воспроизводимым запуском. MVP считается выполненным, если команда на защите демонстрирует:

1. **`docker-compose up --build`** поднимает **FastAPI** (прогноз) и **MLflow** (эксперименты) без ручных шагов.
2. Сервис принимает историю ряда (JSON / CSV) и возвращает прогноз на горизонт $h$; `/health` показывает версию модели и дату обучения.
3. **`data/` и `notebooks/`** — воспроизводимый EDA с документацией.
4. **Скрипт симуляции** (`monitoring/simulate_drift.py` или аналог) — реакция системы на искажённые данные.
5. **Инженерно-технический отчёт**: бизнес-контекст; обоснование метрик (MAE / MAPE / quantile loss при асимметрии); сводная таблица экспериментов; схема MLOps-архитектуры.
6. Процедура **retraining** описана в `docs/monitoring.md` и продемонстрирована или воспроизводима.

На live demo (**MLOps**, с поддержкой ML Engineer) запуск занимает **≤ 5 мин** по `DEPLOY.md`.  
**Data Analyst** подтверждает соответствие входных данных pipeline; **ML Researcher** — метрики и валидацию.

---

## 8. Управление проектом

### Инструменты

- **Git** — feature branches, PR, обязательный review.
- **Issues** — метки: `role:data`, `role:ml`, `role:mlops`, `milestone:LR03` …
- **Stand-up** — еженедельно, 30 мин, фасилитация **координатора**.

### Контрольные точки

| Checkpoint | Вопрос | Кто отвечает |
|------------|--------|--------------|
| После ЛР1 | Достаточно ли данных для горизонта $h$? | Data Analyst |
| После ЛР4 | Beat baseline? | ML Researcher |
| После ЛР8 | Модель зафиксирована для deploy? | ML Engineer + MLOps |
| Перед PRC | Demo без ручных правок? | MLOps |

### Типичные риски

| Риск | Митигация |
|------|-----------|
| Free-rider | RACI + peer-review + git log |
| «Координатор делает всё» | Координатор не подменяет R по milestones |
| Data bottleneck | ML Engineer помогает на ЛР2; ранний synthetic fallback |
| MLOps только в конце | MLOps участвует с ЛР8 (контракт API, формат model artifact) |
| Переобучение на test | ML Researcher ведёт единый `docs/validation.md` |

---

## 9. Формат сдачи

### Milestones ЛР1–10

По [шаблону](templates/milestone_report.md): 3–5 стр. на milestone + commit hash. Раздел подписывается **ответственным (R)**; координатор проверяет целостность перед сдачей.

### Финальная сдача (PRC)

| Компонент | Формат | Роль |
|-----------|--------|------|
| Презентация | PDF, 12–15 слайдов, **10 мин** доклад | Координатор + все |
| Итоговый отчёт | PDF, 15–25 стр. | Координатор (сборка) |
| Репозиторий | Доступ преподавателю | все |
| Demo | Live ≤ 7 мин | MLOps |
| Peer-review | LMS, 48–72 ч после защиты | все |

### Защита

- **15 мин** — презентация (**10 мин** доклад + вопросы; каждый ≥ 2 мин по зоне роли).
- **5–10 мин** — вопросы комиссии / партнёра.
- **5 мин** — live demo (**MLOps**).

---

## 10. Использование LLM и внешних ресурсов

- LLM — для изучения API, черновиков, отладки.
- Код и текст на защите должны быть **понятны исполнителю роли**.
- README: раздел «Использованные инструменты».
- Данные партнёра не в публичные LLM.

---

## 11. Критерии оценки

Подробная rubric: [rubrics.md](rubrics.md). Веса дисциплины: [grading.md](../docs/grading.md).

Проект даёт **35 %** через PRC (защита + peer-review) и **40 %** через milestones ЛР1–10.

$$\text{Балл}_{PRC\_итог} = 35\% \times \frac{B_{комиссии}}{100} \times k_{peer}$$

**Балл комиссии (100 %):**

| Критерий | Вес |
|----------|-----|
| Полнота функциональности MVP | 30 % |
| Качество кода и архитектура | 20 % |
| Мониторинг, устойчивость и ретренинг | 20 % |
| Презентация и доклад | 15 % |
| Техническая документация | 15 % |

Если студент не участвовал в разработке инфраструктуры (роль MLOps), итоговая оценка за PRC может быть снижена через \(k_{peer}\), даже при высоком командном балле.

| Уровень | Суть |
|---------|------|
| **Базовый** | Все milestones сданы; роли соблюдены; MVP работает через docker-compose |
| **Средний** | Сравнение моделей, FastAPI + MLflow, осмысленный вклад каждой роли |
| **Продвинутый** | CI/CD, мониторинг drift, retraining, согласованный roadmap с партнёром |

Индивидуальный вклад учитывается через [peer-review](peer_review.md) (\(k_{peer}\) 0,7–1,3).

---

## 12. Полезные ссылки

- Milestones: [M1](../M1-analysis-and-preparation/) · [M2](../M2-statistical-and-ML-models/) · [M3](../M3-neural-networks/) · [M4](../M4-infrastructure-and-deployment/)
- [Руководство преподавателя](instructor_guide.md)
- [Peer-review](peer_review.md)
- [Шаблон устава](templates/project_charter.md)
